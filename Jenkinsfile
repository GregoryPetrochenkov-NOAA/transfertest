pipeline {
  agent {
    node {
      label 'project:any'
    }
  }
  stages {
    stage('Set Build Description') {
      steps {
        script {
          currentBuild.description = "Deploy to ${env.DEPLOY_STAGE}"
        }
      }
    }
    stage('Clean Workspace') {
      steps {
        cleanWs()
      }
    }
    stage('Git Clone') {
      steps {
        checkout([
            $class: 'GitSCM',
            branches: [[name: '*/master']],
            doGenerateSubmoduleConfigurations: false,
            extensions: [],
            submoduleCfg: [],
            userRemoteConfigs: [[credentialsId: 'CIDA-Jenkins-GitHub',
            url: 'https://github.com/gpetrochenkov-usgs/transfertest.git']]])
      }
    }

    stage('Transfer') {
      steps {
          sh '''mkdir $WORKSPACE/wmadata
            mkdir $WORKSPACE/wmadata/dumps

              /usr/local/bin/aws s3 cp s3://owi-common-resources/resources/InstallFiles/liquibase/liquibase-$LIQUIBASE_VERSION.tar.gz $WORKSPACE/wmadata/liquibase.tar.gz
              /usr/bin/tar xzf $WORKSPACE/wmadata/liquibase.tar.gz --overwrite -C $WORKSPACE/wmadata
              /usr/local/bin/aws s3 cp s3://owi-common-resources/resources/InstallFiles/postgres/$JDBC_JAR $WORKSPACE/wmadata/lib/$JDBC_JAR
              /usr/local/bin/aws s3 cp s3://test-scnoble/gagesii.pgdump.gz $WORKSPACE/wmadata/dumps/gagesii.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/huc08.pgdump.gz $WORKSPACE/wmadata/dumps/huc08.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/huc12.pgdump.gz $WORKSPACE/wmadata/dumps/huc12.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/huc12all.pgdump.gz $WORKSPACE/wmadata/dumps/huc12all.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/huc12agg.pgdump.gz $WORKSPACE/wmadata/dumps/huc12agg.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/nhdarea.pgdump.gz $WORKSPACE/wmadata/dumps/nhdarea.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/nhdflowline_network.pgdump.gz $WORKSPACE/wmadata/dumps/nhdflowline_network.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/nhdwaterbody.pgdump.gz $WORKSPACE/wmadata/dumps/nhdwaterbody.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/catchmentsp.pgdump.gz $WORKSPACE/wmadata/dumps/catchmentsp.pgdump.gz
              /usr/local/bin/aws s3 cp s3://test-scnoble/gagesII.gpkg $WORKSPACE/wmadata/dumps/gagesII.gpkg
              /usr/local/bin/aws s3 cp s3://test-scnoble/huc12agg.gpkg $WORKSPACE/wmadata/dumps/huc12agg.gpkg
              /usr/local/bin/aws s3 cp s3://test-scnoble/us_historical_counties.gpkg $WORKSPACE/wmadata/dumps/us_historical_counties.gpkg

              for file in ./{dumps,data}/*

              do
              test=$(basename $file)
              filename="${test%.*}"
              curl -u ''$artuser:$artpass'' -T $file "https://artifactory.wma.chs.usgs.gov/artifactory/wma-binaries/lfs/api/nwc2wmadataprep/"$(basename $file)""

              done


            '''
      }
    }
  }
}
