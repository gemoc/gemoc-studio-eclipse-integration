// Pipeline using declarative syntax
// you can fall down to script syntax using the script { } command 
pipeline {
	agent   {
		kubernetes {
		label 'gemoc-buildpod'
		defaultContainer 'jnlp' 
		// no cat command as the same in migration ... replace jnlp image ...
		yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - image: gemoc/gemoc-jenkins-fat-agent:2023-06-14
    name: 'jnlp'
    resources:
      limits:
        memory: '4Gi'
        cpu: '2000m'
      requests:
        memory: '4Gi'
        cpu: '1000m'
    securityContext:
      privileged: false
    tty: true
    volumeMounts:
    - mountPath: '/home/jenkins/.m2/toolchains.xml'
      name: 'toolchains-xml'
      readOnly: true
      subPath: 'toolchains.xml'
    - mountPath: '/opt/tools'
      name: 'volume-0'
      readOnly: false
    - mountPath: '/home/jenkins/.m2/repository'
      name: 'volume-3'
      readOnly: false
    - mountPath: '/home/jenkins/.m2/settings-security.xml'
      name: 'settings-security-xml'
      readOnly: true
      subPath: 'settings-security.xml'
    - mountPath: '/home/jenkins/.m2/settings.xml'
      name: 'settings-xml'
      readOnly: true
      subPath: 'settings.xml'
    - mountPath: '/home/jenkins/.ssh'
      name: 'volume-1'
      readOnly: false
    - mountPath: '/home/jenkins/agent'
      name: 'workspace-volume'
      readOnly: false
    workingDir: '/home/jenkins/agent'
  nodeSelector: {}
  restartPolicy: 'Never'
  volumes:
  - name: 'settings-security-xml'
    secret:
      items:
      - key: 'settings-security.xml'
        path: 'settings-security.xml'
      secretName: 'm2-secret-dir'
  - name: 'volume-0'
    persistentVolumeClaim:
      claimName: 'tools-claim-jiro-gemoc'
      readOnly: true
  - configMap:
      items:
      - key: 'toolchains.xml'
        path: 'toolchains.xml'
      name: 'm2-dir'
    name: 'toolchains-xml'
  - emptyDir:
      medium: ''
    name: 'volume-2'
  - configMap:
      name: 'known-hosts'
    name: 'volume-1'
  - name: 'settings-xml'
    secret:
      items:
      - key: 'settings.xml'
        path: 'settings.xml'
      secretName: 'm2-secret-dir'
  - emptyDir:
      medium: ''
    name: 'workspace-volume'
  - emptyDir:
      medium: ''
    name: 'volume-3'
'''
		}
	}
	options {
		//skipDefaultCheckout(true) // avoid default checkout implied by declarative pipeline (we do it in the prepare stage)
		buildDiscarder( logRotator(numToKeepStr:'60', artifactNumToKeepStr: '5'))
		disableConcurrentBuilds()
		timeout(time: 5, unit: 'HOURS')   // timeout on whole pipeline job
	}
	tools {
	    // cf. https://wiki.eclipse.org/Jenkins#Tools_.28and_locations_on_the_default_JNLP_agent_container.29
		// cf. https://wiki.eclipse.org/Jenkins#Apache_Maven
        // maven 'apache-maven-latest'
		maven 'apache-maven-3.8.6'
        jdk 'openjdk-jdk17-latest'
	}
	environment {
		DOWNLOAD_FOLDER = "/home/data/httpd/download.eclipse.org/gemoc"
		STORAGE_SERVER="genie.gemoc@projects-storage.eclipse.org"
	}
	stages {
		
		stage('Prepare') {
			steps {	
				echo 'Content of the workspace before Checkout'
				sh "ls -lsa"
				sh "echo $HOME"
				sh "ls -lsa $HOME"
				sh "ls -lsa $HOME/agent"
				sh "ls -lsa $JAVAFX_HOME"
				
				// Wipe the workspace so we are building completely clean
	/*			deleteDir()
				//cleanWs()

				// Get code from GitHub repositories				
				// this will check if there is a branch with the same name as the current branch (ie. the branch containing this Jenkinsfile) and use that for the checkout, but if there is no
				// branch with the same name it will fall back to the master branch
				dir('gemoc-studio') {
					//	checkout resolveScm(source: git('https://github.com/eclipse/gemoc-studio.git'), targets: [BRANCH_NAME,'master'])
					script {
						def gemocstudioScm = resolveScm source: [$class: 'GitSCMSource', credentialsId: '', id: '_', remote: 'https://github.com/eclipse/gemoc-studio.git', traits: [[$class: 'BranchDiscoveryTrait'], [$class: 'LocalBranchTrait']]], targets: [BRANCH_NAME, 'master']
						checkout gemocstudioScm
					}
				}
				dir('gemoc-studio-modeldebugging') {
					script {
						def gemocstudiomodeldebuggingScm = resolveScm source: [$class: 'GitSCMSource', credentialsId: '', id: '_', remote: 'https://github.com/eclipse/gemoc-studio-modeldebugging.git', traits: [[$class: 'BranchDiscoveryTrait'], [$class: 'LocalBranchTrait']]], targets: [BRANCH_NAME, 'master']
						checkout gemocstudiomodeldebuggingScm
					}
				}
				dir('gemoc-studio-execution-ale') {
					script {
						def gemocstudiomodeldebuggingScm = resolveScm source: [$class: 'GitSCMSource', credentialsId: '', id: '_', remote: 'https://github.com/eclipse/gemoc-studio-execution-ale.git', traits: [[$class: 'BranchDiscoveryTrait'], [$class: 'LocalBranchTrait']]], targets: [BRANCH_NAME, 'master']
						checkout gemocstudiomodeldebuggingScm
					}
				}
				dir('gemoc-studio-moccml') {
					script {
						def gemocstudiomoccmlScm = resolveScm source: [$class: 'GitSCMSource', credentialsId: '', id: '_', remote: 'https://github.com/eclipse/gemoc-studio-moccml.git', traits: [[$class: 'BranchDiscoveryTrait'], [$class: 'LocalBranchTrait']]], targets: [BRANCH_NAME, 'master']
						checkout gemocstudiomoccmlScm
					}
				}
				dir('gemoc-studio-execution-moccml') {
					script {
						def gemocstudioexecutionmoccmlScm = resolveScm source: [$class: 'GitSCMSource', credentialsId: '', id: '_', remote: 'https://github.com/eclipse/gemoc-studio-execution-moccml.git', traits: [[$class: 'BranchDiscoveryTrait'], [$class: 'LocalBranchTrait']]], targets: [BRANCH_NAME, 'master']
						checkout gemocstudioexecutionmoccmlScm
					}
				}
		*/	//	echo 'Content of the workspace after Checkout'
			//	sh "ls -lsa"
				sh "chmod 777 ./gemoc-studio/dev_support/jenkins/showGitBranches.sh"
				sh "./gemoc-studio/dev_support/jenkins/showGitBranches.sh ."
				sh "cat .gitmodules"
			}
		}
		stage('Generate protocols') {
			steps { 
				dir ('gemoc-studio-modeldebugging/protocols/generators/ts/JSONSchema2APIProtocolGenerator') {
					sh 'npm config ls'
					sh 'npm install'
					sh 'npm run build'
					sh 'npm run generate'
		        }
			}
	 	}
		stage('Pomfirst Build') {
			steps { 
				script {	
					withEnv(["MAVEN_OPTS=-Xmx2000m -XshowSettings:vm"]){
						dir ('gemoc-studio/dev_support/pomfirst_full_compilation') {
							sh "mvn -Dmaven.test.failure.ignore \
									dependency:tree dependency:analyze dependency:analyze-dep-mgt \
									clean install \
									--errors --show-version --batch-mode "
						}      
					}
				}
			}
	 	}
	 	/* stage('Update targetplatform') {
			steps { 
				script {	
					dir ('gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.targetplatform') {
						sh "./update_target.sh"
					} 
				}
			}
	 	} */
		stage('Tycho Build and unit test') {
			steps { 
				script {	
					def studioVariant
					if(  env.JENKINS_URL.contains("https://ci.eclipse.org/gemoc/")){
						studioVariant = "Official build"
					} else {
						studioVariant = "${JENKINS_URL}"
					}
					// Run the maven build and unit tests only  
					// maven requires some ram to build the update site and product
					withEnv(["STUDIO_VARIANT=${studioVariant}","BRANCH_VARIANT=${BRANCH_NAME}",
						"MAVEN_OPTS=-Xmx2048m -XshowSettings:vm -Duser.home=/home/jenkins"]){
						dir ('gemoc-studio/dev_support/tycho_full_compilation') {
							sh 'printenv'         
							sh "python3 ~/memory-monitor-per-process.py > build_process_mem.log &\
								mvn -Dmaven.test.failure.ignore \"-Dstudio.variant=${studioVariant}\" -Dbranch.variant=${BRANCH_VARIANT} \
									-Djava.awt.headless=true \
									--projects !../../gemoc_studio/tests/org.eclipse.gemoc.studio.tests.system.lwb,!../../gemoc_studio/tests/org.eclipse.gemoc.studio.tests.system.mwb,!../../gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite\
									clean install --errors --show-version --batch-mode "
						}      
					}
				}
			}
			post {
				always {
					junit '**/target/surefire-reports/TEST-*.xml'
					archiveArtifacts 'gemoc-studio/dev_support/tycho_full_compilation/target/**, **/screenshots/**, **/.metadata/.log, **/build_process_mem.log' 
				}
			}
	 	}
	 	stage('Tycho Build product') {
			steps { 
				script {	
					def studioVariant
					if(  env.JENKINS_URL.contains("https://ci.eclipse.org/gemoc/")){
						studioVariant = "Official build"
					} else {
						studioVariant = "${JENKINS_URL}"
					}
					// Run the maven build and unit tests only  
					// maven requires some ram to build the update site and product
					withEnv(["STUDIO_VARIANT=${studioVariant}","BRANCH_VARIANT=${BRANCH_NAME}",
						"MAVEN_OPTS=-Xmx2048m -XshowSettings:vm -Duser.home=/home/jenkins"]){
						dir ('gemoc-studio/dev_support/tycho_full_compilation') {
							sh 'printenv'         
							sh "mvn -Dmaven.test.failure.ignore \"-Dstudio.variant=${studioVariant}\" -Dbranch.variant=${BRANCH_VARIANT} \
									-Djava.awt.headless=true \
									--projects ../../gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.targetplatform,../../gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite\
									clean install --errors --show-version --batch-mode "
						}      
					}
				}
			}
			post {
				always {
					junit '**/target/surefire-reports/TEST-*.xml'
				    archiveArtifacts 'gemoc-studio/dev_support/tycho_full_compilation/target/**, **/screenshots/**, **/.metadata/.log, **/*_process_mem.log' 
				}
			}
	 	}
		stage('Tycho System test') {
			steps { 
				script {	
					def studioVariant
					if(  env.JENKINS_URL.contains("https://hudson.eclipse.org/gemoc/")){
						studioVariant = "Official build"
					} else {
						studioVariant = "${JENKINS_URL}"
					}
					// Run the maven system tests only  
					// allocate less RAM to maven in order to give more to the UI test JVM
					withEnv(["STUDIO_VARIANT=${studioVariant}","BRANCH_VARIANT=${BRANCH_NAME}",
						"MAVEN_OPTS=-Xmx1200m  -XshowSettings:vm"]){
						dir ('gemoc-studio/dev_support/tycho_full_compilation') {         
							wrap([$class: 'Xvnc', takeScreenshot: false, useXauthority: true]) {
							sh 'printenv'
							sh 'touch /tmp/stop-ffmpeg'
							sh 'mkdir -p target'  
							// initiate the recording log with a timestamp
							sh 'date --rfc-3339=s  > target/system_test_timeline.log'
							/*
							-crf : quality the lower the better
							-r : frame per seconds
							-preset ultrafast  : reduce memory footpring
							*/   
							sh "ffmpeg -f x11grab -video_size 1024x768 -i $DISPLAY -vcodec libx264 -tune stillimage -preset ultrafast -crf 23 -r 12 -pix_fmt yuv420p target/system_test.mp4 </tmp/stop-ffmpeg  >target/VideoCapture.log 2>>target/VideoCapture_err.log &"
							// use linux timeout in order to continue the video capture
							// capture the returnStatus in order to make sure to stop ffmepg even if an error occured
							//def status = sh(returnStatus: true, script: "top -c -d 2 -w128 -b& \
							def status = sh(returnStatus: true, script: "timeout -s KILL 60m \
								mvn -Dmaven.test.failure.ignore \"-Dstudio.variant=${studioVariant}\" -Dbranch.variant=${BRANCH_VARIANT} \
									--projects ../../gemoc_studio/tests/org.eclipse.gemoc.studio.tests.system.lwb,../../gemoc_studio/tests/org.eclipse.gemoc.studio.tests.system.mwb,../../gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.targetplatform\
									verify --errors --show-version --batch-mode ")				
							sh "echo 'q' > /tmp/stop-ffmpeg"
							if (status != 0) {
  								currentBuild.result = 'FAILED'
								error 'SystemTest Timeout'
							}
							}
						}      
					}
				}
			}
			post {
				always {
					junit '**/target/surefire-reports/TEST-*.xml' 
				}
				// archive artifact even if it failed (timeout) or was aborted (in order to debug using the video)
				// because the following steps will be skipped
				aborted {
				    archiveArtifacts 'gemoc-studio/dev_support/tycho_full_compilation/target/**, **/screenshots/**, **/.metadata/.log, **/*process_mem.log'
				}
				failure {
				    archiveArtifacts 'gemoc-studio/dev_support/tycho_full_compilation/target/**, **/screenshots/**, **/.metadata/.log, **/*process_mem.log'				    
				}
			}
	 	}
		stage("Archive in Jenkins") {
			steps {
				echo "archive artifact"
				archiveArtifacts 'gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/products/*.zip, gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/products/*.tar.gz, gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/repository/**, gemoc-studio/docs/org.eclipse.gemoc.studio.doc/target/publish/**, gemoc-studio/dev_support/tycho_full_compilation/target/**, **/screenshots/**, **/.metadata/.log, **/process_mem.log'
			}
		}
		stage('Web upload') {
			when {
        		// skip this stage unless on Master branch
        		branch "master"
			}
			steps {
				sshagent(['projects-storage.eclipse.org-bot-ssh']) {
					echo "Deploy products to download.eclipse.org"
					sh '''
						ssh ${STORAGE_SERVER} rm -rf ${DOWNLOAD_FOLDER}/packages/nightly
						ssh ${STORAGE_SERVER} mkdir -p ${DOWNLOAD_FOLDER}/packages/nightly
					'''
					sh '''
						scp -r gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/products/*.zip ${STORAGE_SERVER}:${DOWNLOAD_FOLDER}/packages/nightly
						scp -r gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/products/*.tar.gz ${STORAGE_SERVER}:${DOWNLOAD_FOLDER}/packages/nightly
						scp -r gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/products/index.html ${STORAGE_SERVER}:${DOWNLOAD_FOLDER}/packages/nightly
					'''
					echo "Deploy updatesite to download.eclipse.org"
					sh '''
						ssh ${STORAGE_SERVER} rm -rf   ${DOWNLOAD_FOLDER}/updates/nightly
						ssh ${STORAGE_SERVER} mkdir -p ${DOWNLOAD_FOLDER}/updates/nightly
					'''
					sh '''
						scp -r gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/repository/* ${STORAGE_SERVER}:${DOWNLOAD_FOLDER}/updates/nightly
					'''
					//sh 'zip -R   ${DOWNLOAD_FOLDER}/updates/nightly/repository.zip gemoc-studio/gemoc_studio/releng/org.eclipse.gemoc.gemoc_studio.updatesite/target/repository/*'
					
					
					echo "Deploy documentation archive to download.eclipse.org"
					sh '''
						ssh ${STORAGE_SERVER} rm -rf   ${DOWNLOAD_FOLDER}/docs/nightly
						ssh ${STORAGE_SERVER} mkdir -p ${DOWNLOAD_FOLDER}/docs/nightly
					'''
					sh '''
						scp -r gemoc-studio/docs/org.eclipse.gemoc.studio.doc/target/publish/webhelp/* ${STORAGE_SERVER}:${DOWNLOAD_FOLDER}/docs/nightly
					'''
					//sh 'mkdir -p ${DOWNLOAD_FOLDER}/docs/nightly/archive'
					//sh 'zip -R   ${DOWNLOAD_FOLDER}/docs/nightly/archive/studio-docs.zip gemoc-studio/docs/org.eclipse.gemoc.studio.doc/target/publish/webhelp/*'
				}
			}
		} 
		stage('deploy to nexus') {
			when {
        		// skip this stage unless on Master branch
        		branch "master"
			}
			steps { 
				script {	
					withEnv(["MAVEN_OPTS=-Xmx2000m -XshowSettings:vm -Duser.home=/home/jenkins"]){
						dir ('gemoc-studio/dev_support/pomfirst_full_compilation') {
							sh "mvn -Dmaven.test.failure.ignore \
									-DskipTests \
									-DaltReleaseDeploymentRepository=repo.eclipse.org::default::https://repo.eclipse.org/content/repositories/gemoc-releases/ \
									-DaltSnapshotDeploymentRepository=repo.eclipse.org::default::https://repo.eclipse.org/content/repositories/gemoc-snapshots/ \
									-DaltDeploymentRepository=repo.eclipse.org::default::https://repo.eclipse.org/content/repositories/gemoc-snapshots/ \
									deploy \
									--errors --show-version"
						}      
					}
				}
			}
	 	}
	}
	post { 
		// send mail to main maintainers for unstable builds of master branch
		// note: should also send mail to culprits but currently 
        //       sendToIndividuals doesn't work due to https://issues.jenkins-ci.org/browse/JENKINS-43202
		always {
			script { 
				if(  "${BRANCH_NAME}".equals("master")){
					// note: sendToIndividuals doesn't work due to https://issues.jenkins-ci.org/browse/JENKINS-43202
					step([$class: 'Mailer', notifyEveryUnstableBuild: true, recipients: "didier.vojtisek@inria.fr, erwan.bousse@ls2n.fr", sendToIndividuals: true])
				}
			}  
		}
		// changed { }
		unstable {
			echo 'Unstable' 
		}
		failure {
			echo 'Failure' 
		}
	}
}
