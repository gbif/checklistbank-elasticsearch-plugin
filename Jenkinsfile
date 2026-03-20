@Library('gbif-common-jenkins-pipelines') _

pipeline {
  agent any
  tools {
    maven 'Maven 3.9.9'
    jdk 'OpenJDK22'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
    skipStagesAfterUnstable()
    timestamps()
    disableConcurrentBuilds()
  }
  triggers {
    snapshotDependencies()
  }
  parameters {
    separator(name: "release_separator", sectionHeader: "Release Main Project Parameters")
    booleanParam(name: 'RELEASE', defaultValue: false, description: 'Do a Maven release')
    string(name: 'RELEASE_VERSION', defaultValue: '', description: 'Release version (optional)')
    string(name: 'DEVELOPMENT_VERSION', defaultValue: '', description: 'Development version (optional)')
    booleanParam(name: 'DRY_RUN_RELEASE', defaultValue: false, description: 'Dry Run Maven release')
  }
  environment {
    JETTY_PORT = utils.getPort()
  }
  stages {

    stage('Maven build') {
       when {
        allOf {
          not { expression { params.RELEASE } };
        }
      }
      steps {
        script {
          env.JAVA_HOME = tool(name: 'OpenJDK22', type: 'jdk')
        }
        withEnv(["PATH+JAVA=${env.JAVA_HOME}/bin"]) {
        withMaven(
            globalMavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.GlobalMavenSettingsConfig1387378707709',
            mavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.MavenSettingsConfig1396361652540',
            traceability: true) {
                sh '''
                   echo "JAVA_HOME=$JAVA_HOME"
                   java -version
                   mvn -version
                   mvn -B -U clean package dependency:analyze
                  '''
              }
        }
      }
    }
    stage('Snapshot to nexus') {
        when {
            allOf {
                not { expression { params.RELEASE } };
                anyOf {
                    branch 'dev';
                }
            }
        }
        steps {
            script {
              env.JAVA_HOME = tool(name: 'OpenJDK22', type: 'jdk')
            }
            withEnv(["PATH+JAVA=${env.JAVA_HOME}/bin"]) {
            withMaven (
                jdk: 'OpenJDK22',
                maven: 'Maven 3.9.9',
                globalMavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.GlobalMavenSettingsConfig1387378707709',
                mavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.MavenSettingsConfig1396361652540',
                traceability: true){
                    sh 'mvn -B -DskipTests deploy'
                }
            }
        }
    }
    stage('Maven release') {
      when {
          allOf {
              expression { params.RELEASE };
              branch 'master';
          }
      }
      environment {
          RELEASE_ARGS = utils.createReleaseArgs(params.RELEASE_VERSION, params.DEVELOPMENT_VERSION, params.DRY_RUN_RELEASE)
      }
      steps {
          script {
            env.JAVA_HOME = tool(name: 'OpenJDK22', type: 'jdk')
          }
          withEnv(["PATH+JAVA=${env.JAVA_HOME}/bin"]) {
          withMaven(
            jdk: 'OpenJDK22',
            maven: 'Maven 3.9.9',
            globalMavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.GlobalMavenSettingsConfig1387378707709',
            mavenSettingsConfig: 'org.jenkinsci.plugins.configfiles.maven.MavenSettingsConfig1396361652540',
            traceability: true) {
              git 'https://github.com/gbif/checklistbank-elasticsearch-plugin.git'
              sh '''
                mvn -B -Dresume=false release:prepare release:perform $RELEASE_ARGS
                '''
            }
          }
      }
    }
  }
    post {
      success {
        echo 'Pipeline executed successfully!'
      }
      failure {
        echo 'Pipeline execution failed!'
    }
  }
}
