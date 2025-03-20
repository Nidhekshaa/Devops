pipeline {
    agent any
    stages {
stage("Restore npm Packages") {
    steps {
        writeFile file: "next-lock.cache", text: "$GIT_COMMIT"
 
        cache(caches: [
            arbitraryFileCache(
                path: "node_modules",
                includes: "**/*",
                cacheValidityDecidingFile: "package-lock.json"
            )
        ]) {
            sh "npm install"
        }
    }
}

	 stage('Setup NPM and Vagrant Env.') {
	  parallel { 
	   stage('npm Install') { 
            steps {
                sh 'npm install'
            }
            } 
           stage('Vagrant Env. Setup for Infra'){ 
	    steps { 
		sh 'vagrant plugin install virtualbox_WSL2' 
                sh 'vagrant plugin install vagrant-scp'
		} 
           } 
         } 
        }

	stage('Build the Application') { 
		steps { 
			sh 'ng build --configuration production'
		} 
	} 

	stage('Run Lint and Prettier') { 
		parallel { 
 	        stage('Lint Checks') { 
		steps { 
		   sh 'ng add @angular-eslint/schematics' 
		   sh 'npm run lint'  
		}
		}
 	        stage('Pretty Code') { 
		steps { 
		   sh 'npm run prettier:fix'  
		}
		}
               }  
	} 
	stage ('Deploy Infra') { 
		steps {
		sh 'vagrant up' 
		} 
	}

	stage ('Deploy Application') { 
		steps { 
		echo 'All done'
withCredentials([usernamePassword(credentialsId: 'SSHCredentials', passwordVariable: 'SSHUserPass', usernameVariable: 'SSHUserName')]) {
		sh 'sshpass -p $SSHUserPass ssh-copy-id -o StrictHostKeyChecking=no ${SSHUserName}@192.168.56.105'
}
		} 
	} 
	stage('Test Application') {
		parallel { 
 	        stage('Chrome Testing') { 
		steps { 
		   echo 'Test on Chrome Browsers'
		}
		}
 	        stage('Edge Testing') { 
		steps { 
		   echo 'Test on Edge Browsers'
		}
		}
 	        stage('Firefox Testing') { 
		steps { 
		   echo 'Test on Firefox  Browsers'
		}
		}
	
		} 
	} 
 
        }
    }
