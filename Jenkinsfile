pipeline {
	agent any
	environment {
		PATH			=	"${env.PATH}"
		projectName		=	"ci-cd-template"
		appsFolder		=	"/tmp/apps/"
		chAppName		=	"xpto-app-test"
		chEnvironemnt	=	"Sandbox"
		chCredentials   =  credentials('anypoint-platform-connected-app')
	}
	
	parameters {
	    extendedChoice( 
	        name: 'SKIP', 
	        defaultValue: '',
	        description: 'Skip CloudHub Deployment Verification', 
	        multiSelectDelimiter: ',', 
	        quoteValue: false, 
	        saveJSONParameterToFile: false, 
	        type: 'PT_CHECKBOX', 
	        value:'Skip?', 
	        visibleItemCount: 10)
	    extendedChoice( 
	        name: 'BUSINESS_GROUP',
	        defaultValue: 'Demos',
	        multiSelectDelimiter: ',', 
	        quoteValue: false, 
	        saveJSONParameterToFile: false, 
	        type: 'PT_RADIO', 
	        value:'Geral,Demos', 
	        visibleItemCount: 10)
        extendedChoice(
            name: 'REGION',
            description: 'Cloudhub Region',
            multiSelectDelimiter: ',',
            visibleItemCount: 5,
            quoteValue: false,
            type: 'PT_SINGLE_SELECT',
	        value:'eu-central-1,eu-west-1,eu-west-2',
	        descriptionPropertyValue:'Europe (Frankfurt):  *.de-c1.cloudhub.io,Europe (Ireland):  *.ir-e1.cloudhub.io,UK (London):  *.	uk-e1.cloudhub.io',
	        defaultValue:'eu-central-1'
            )
        extendedChoice(
            name: 'SIZE',
            description: 'Mule Worker Size',
            multiSelectDelimiter: ',',
            visibleItemCount: 5,
            quoteValue: false,
            type: 'PT_SINGLE_SELECT',
	        value:'MICRO,SMALL,MEDIUM,LARGE,XLARGE,XXLARGE,4XLARGE',
	        descriptionPropertyValue:'0.1 vCores (500 MB memory),0.2 vCores (1 GB memory),1 vCore (1.5 GB memory),2 vCores (3.5 GB memory),4 vCores (7.5 GB memory),8 vCores (15 GB memory),16 vCores (32 GB memory)',
	        defaultValue:'MICRO'
            )
        extendedChoice(
            name: 'WORKERS',
            description: 'Number of Workers (2+ workers are recommended for added reliability)',
            multiSelectDelimiter: ',',
            visibleItemCount: 5,
            quoteValue: false,
            type: 'PT_SINGLE_SELECT',
	        value:'1,2,3,4,5,6,7,8',
	        defaultValue:'1'
            )
	}

   	stages {

		stage('Build and Test') {
			steps {
				echo "Starting Build and Testing"
 				sh "mvn clean package test"

				script {
					publishHTML (target : [allowMissing: false,
						alwaysLinkToLastBuild: true,
						keepAll: true,
						reportDir: 'target\\site\\munit\\coverage',
						reportFiles: 'summary.html',
						reportName: 'MUnit App Coverage',
						reportTitles: 'MUnit App Coverage']
					 )
				}
				echo "Built and tested."

 			}
		}

		stage('Release Version') {
			steps {
				echo "Starting Prepare Release and Create Tag"

 				sh 'mvn release:clean release:prepare'
				script {
			    	props = readProperties(file: "${JENKINS_HOME}\\workspace\\${projectName}\\release.properties")
					releaseVersion = props['scm.tag']
 					echo "Maven Release Version"
 					echo "${releaseVersion}"
	                newVersion = sh(
				        returnStdout: true, 
				        script: "echo ${releaseVersion} | cut -f4 -d '-'"
					).trim()
 					echo "Maven New Release Version"
	                echo "${newVersion}"
 				}

				echo "Prepared Release and Tag created: ${currentBuild.currentResult} - ${releaseVersion}"

				echo "Starting Deploy to Nexus"
 				sh 'mvn release:perform'

				echo "Deployed to Nexus."

			}
		}

		stage('Download Artifact') {
			steps {
				echo "Starting Artifact Download"
				script {
					pom = readMavenPom file: 'pom.xml'
 				}
 				sh "mvn dependency:copy -Dartifact=${pom.groupId}:${pom.artifactId}:${newVersion}:jar:mule-application -DoutputDirectory=${appsFolder} -Dmdep.overWriteReleases=true"
				echo "Artifact Downloaded."
			}
		}

		stage('Deploy to Mulesoft') {
			steps {
				echo "Starting Deploy"
				script {
					
					def varSkip		= "${params.SKIP}"
					if ( varSkip == '' ) {
					    varSkip = "false";
					} else {
					    varSkip = "true";
					}

					def varBusGroup	= "${params.BUSINESS_GROUP}"
					if ( varBusGroup == 'Geral' ) {
					    varBusGroup = "40d50723-0b77-4a58-ba88-6350ff75ccd5";
					} else if ( varBusGroup == 'Demos' ) {
					    varBusGroup = "a6c3ad5a-a332-46b9-8189-199df88db98f";
					} else {
					    varBusGroup = "a6c3ad5a-a332-46b9-8189-199df88db98f";
					}

					def varRegion	= "${params.REGION}"
					def varWorkers	= "${params.WORKERS}"
					def varWorkerTp	= "${params.SIZE}"

					def varEnv		= "${chEnvironemnt}"
	 				def muleAppName = "${chAppName}"

	 				def muleJarName = "${appsFolder}${releaseVersion}-mule-application.jar"
	 				sh 'mvn mule:deploy -Dmule.artifact=' + muleJarName + ' -Dapp.runtime=\'${deploy.runtime}\' -DciCdAppId=$chCredentials_USR -DciCdAppSecret="$chCredentials_PSW" -Dcloudhub.application.name=' + muleAppName + ' -Denvironment=' + varEnv + ' -Dregion=' + varRegion + ' -Dworkers=' + varWorkers + ' -DworkerType=' + varWorkerTp + ' -DbusinessGroupId=' + varBusGroup + ' -DskipDeployCheck=' + varSkip
	 			}
				echo "Performed Deploy."
			}
		}

		stage('Integration Test') {
			steps {
				sh "newman run ./src/test/resources/test-case.postman_collection.json -r htmlextra --reporter-htmlextra-export  ./target/newman-report.html --reporter-htmlextra-logs"
				script {
					publishHTML (target : [allowMissing: false,
						alwaysLinkToLastBuild: true,
						keepAll: true,
						reportDir: 'target',
						reportFiles: 'newman-report.html',
						reportName: 'Integrated Test (Newman)',
						reportTitles: 'Integrated Test (Newman)']
					 )
				}

				script {
           			ret = sh(script: "git tag --sort=-v:refname", returnStdout: true).trim().split('\n').toString().replace("[", "").replace("]", "")
           			ret = "props=${ret}"
           			echo ret
           			writeFile(file: "${JENKINS_HOME}/workspace/${projectName}.properties", text: ret)

				}

			}
		}

/*
		stage('Rollback Release') {
			steps {
				echo "Rollback Release START"
 				//sh 'mvn release:rollback release:clean'
				echo "Rollback Release END."
			}
		}
*/
	}
}