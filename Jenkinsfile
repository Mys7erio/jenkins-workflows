pipeline {
    agent any
    
    stages {
        stage('Apply color grading') {
            steps {
                script {
                    sh 'git config --global --add safe.directory /tmp/jenkins-workflows'
                    def allowed_file_extensions = ['mp4']   // will add other extensions soon
                    def new_files = sh(script: 'cd /tmp/jenkins-workflows && git diff-tree --no-commit-id --name-only --diff-filter=A -r HEAD', returnStdout: true)
                                    .trim()
                                    .split('\n')
                                    
                    new_files.each { new_file ->
                        def (name, ext) = new_file.split("\\.")
                        
                        if (allowed_file_extensions.contains(ext)) {
                            def output_file_name = "${name}_colorgraded.${ext}"
                            def command = """
                                ffmpeg -i /tmp/jenkins-workflows/${new_file} \
                                -threads 1 \
                                -vf 'lut3d=file=/tmp/jenkins-workflows/drop_green_on_it.cube' \
                                /tmp/jenkins-workflows/${output_file_name}
                            """
                            
                            echo "Processing file ${new_file}..."
                            sh(command)
                        }
                    }
                }
                

                echo "Finish stage"
            }
        }
    }
}
