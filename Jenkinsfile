def setVersions() {
	sh "mvn versions:set -DnewVersion=$MAJOR_VERSION_NUMBER-$BUILD_NUMBER -DprocessDependencies=false -DprocessParent=true -Dmaven.test.skip=true"
}

def build() {
    sh "mvn clean install -DskipTests=true "
}

def deployCXS() {
	if (env.PUBLISH_TO_CXS_NEXUS == 'true') {
		sh "mvn clean install package deploy:deploy -Pattach-sources,generate-javadoc,maven-release -DskipTests=true -DskipNexusStagingDeployMojo=true -DaltDeploymentRepository=nexus::default::$CXS_NEXUS2_URL"
	} else {
	    sh 'echo skipping CXS deployment'
	}
}

node("cxs-slave-master") {
    echo sh(returnStdout: true, script: 'env')

    configFileProvider(
        [configFile(fileId: '37cb206e-6498-4d8a-9b3d-379cd0ccd99b',  targetLocation: 'settings.xml')]) {
	    sh 'mkdir -p ~/.m2 && sed -i "s|@LOCAL_REPO_PATH@|$WORKSPACE/M2_REPO|g" $WORKSPACE/settings.xml && cp $WORKSPACE/settings.xml -f ~/.m2/settings.xml'
    }

    stage ('Checkout') {
        checkout scm
    }

    stage ('Versioning') {
        setVersions()
    }

    stage ('Build') {
        build()
    }

    stage ('Deploy') {
        deployCXS()
    }
}
