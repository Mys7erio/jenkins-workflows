pipeline {
    agent {
        docker {
            image 'linuxserver/ffmpeg:latest'
            args '-v /jenkins-workflows:/root/jenkins-workflows --entrypoint /bin/bash'
        }
    }
    
    stages {
        stage('Apply color grading') {
            steps {
                script {
                    def allowed_file_extensions = ['mp4']   // will add other extensions soon
                    def new_files = sh(script: 'cd /root/jenkins-workflows && git diff-tree --no-commit-id --name-only --diff-filter=A -r HEAD', returnStdout: true)
                                    .trim()
                                    .split('\n')
                                    
                    new_files.each { new_file ->
                        def (name, ext) = new_file.split("\\.")
                        
                        if (allowed_file_extensions.contains(ext)) {
                            def output_file_name = "${name}_colorgraded.${ext}"
                            def command = """
                                ffmpeg -i /root/jenkins-workflows/${new_file} \
                                -threads 1 \
                                -vf 'lut3d=file=/root/jenkins-workflows/drop_green_on_it.cube' \
                                /root/jenkins-workflows/${output_file_name}
                            """
                            
                            echo "Processing file ${new_file}..."
                            sh(command)
                        }
                    }
                }
            }
        }
    }
}
