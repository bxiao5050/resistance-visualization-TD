node () {
    def workspace = pwd()

    stage ('checkout') {
        git branch: 'master', credentialsId: '99130ab1-7e3b-4305-8748-a342c064d9a8', url: 'http://gitsrv01.royale.com/front-end/system-oversea-new.git'
    }

    stage ('install') {
        try {
            sh 'npm install'
        } catch(err) {
            sh 'echo "npm install error"'
            throw err
            sh 'exit 1'
        }
    }
    stage ('build') {
        try {
            sh """
                rm -rf dist
                npm run build
            """
        } catch(err) {
            sh 'echo "npm run build error"'
            throw err
            sh 'exit 1'
        }
    }
    stage ('package') {
        sh """
            dt=$(date '+%Y%m%d%H%M%S')
            filename=oas-${dt}.zip
            if [ -d dist ]; then
                cd dist && zip -qr ${filename} *
                cd ..
            else
                echo "not found dist"
                exit 1
            fi
        """
    }
    stage ('update') {
        sh """
            src_file=(ls -rht dist/oas-*.zip | head -n 1)
            dest_file=/data/server_new/${src_file#dist/}
            dt=$(date '+%Y%m%d%H%M%S')
            ansible-playbook -i ansible/hosts ansible/deploy.yml -v --extra-var "src_file=${src_file} dest_file=${dest_file} arch_file=oas-${dt}.zip project=oas"
        """
    }
}