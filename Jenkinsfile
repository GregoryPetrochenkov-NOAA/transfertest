pipeline {
  agent {
    node {
      label 'project:any'
    }
  }
  options { timestamps () }
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

              s3url=s3://prod-owi-resources/resources/Application/iow/wmadata-schema
              /usr/local/bin/aws s3 cp $s3url/huc12agg.pgdump.gz $WORKSPACE/wmadata/dumps/huc12agg.pgdump.gz
              /usr/local/bin/aws s3 cp $s3url/nhdflowline_network.pgdump.gz $WORKSPACE/wmadata/dumps/nhdflowline_network.pgdump.gz
              /usr/local/bin/aws s3 cp $s3url/catchmentsp.pgdump.gz $WORKSPACE/wmadata/dumps/catchmentsp.pgdump.gz
              /usr/local/bin/aws s3 cp $s3url/huc12agg.gpkg $WORKSPACE/wmadata/dumps/huc12agg.gpkg

              for file in $WORKSPACE/wmadata/dumps/*

              arturl= "https://artifactory.wma.chs.usgs.gov/artifactory/wma-binaries/lfs/api/nwc2wmadataprep/"
              do
              curl -u ''$artuser:$artpass'' -T $file $arturl$(basename $file)
              done
            '''
      }
    }
  }
}
