# Terraform-Infrastructure-Provisioning-usingError-free-Jenkins-Pipeline

Creating a Jenkins pipeline to deploy infrastructure using Terraform involves several steps, from setting up Jenkins and Terraform to writing the pipeline script. 
Here's a step-by-step guide:

### Prerequisites

1. **Jenkins Installed**: Make sure you have Jenkins installed and running. You can install it on a local machine, VM, or container.
2. **Terraform Installed**: Terraform should be installed on the machine where Jenkins is running.
3. **Version Control System**: Use a version control system like Git to manage your Terraform code.
4. **Cloud Provider Account**: Have an account with a cloud provider like AWS, Azure, or GCP.

### Steps to Install Prerequisites
### Step 1: Install Jenkins

1. **Download and Install Jenkins**
   - Download Jenkins from [the official site](https://www.jenkins.io/download/).
   - Follow the installation instructions for your operating system.

2. **Start Jenkins**
   - Launch Jenkins and complete the initial setup wizard.
   - Install the recommended plugins.

### Step 2: Install Terraform

1. **Download and Install Terraform**
   - Download Terraform from [the official site](https://www.terraform.io/downloads).
   - Follow the installation instructions for your operating system.
   - Ensure `terraform` is in your system’s PATH.


### Step-by-Step Guide for Pipeline

### Step 1: Configure Jenkins for Terraform

1. **Install Plugins**

  - **Pipeline**: Provides the Pipeline DSL and Groovy-based scripting.
  - **Git**: Integrates Git with Jenkins.
  - **Terraform**: Integrates Terraform with Jenkins.
    
   - Go to Jenkins dashboard > Manage Jenkins > Manage Plugins.
   - Install the following plugins:
     - `Pipeline`
     - `Git` (if using Git for version control)
     - `Terraform`
     - `Credentials Binding`

2. **Set Up Credentials**
   - Go to Jenkins dashboard > Manage Jenkins > Manage Credentials.
   - Add credentials (e.g., AWS credentials if deploying on AWS).
     
#### Step 2. Create a New Jenkins Pipeline Job

1. Open Jenkins and click on **New Item**.
2. Select **Pipeline** and give it a name.
3. Click **OK**.


#### Step 3. Configure the Pipeline

1. **Configure the Pipeline**
   - In the pipeline configuration, scroll down to the "Pipeline" section.
   - Select "Pipeline script"  e.g., `Jenkinsfile` 

2. **Pipeline Definition**:
   - Choose **Pipeline script from SCM**.
   - Select **Git**.
   - Provide the **Repository URL**.
   - Specify the **Branch** to build.
  
  
#### Step 4. Write the Jenkinsfile & Create a Jenkins Pipeline

Create a file named `Jenkinsfile` in your repository with the following content:

```groovy
pipeline {
    agent any

    environment {
        // Define environment variables, e.g., AWS credentials
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
    }

    stages {
        stage('Clone repository') {
            steps {
                git 'https://github.com/your-repo/terraform-scripts.git'
            }
        }
        
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        
        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out plan.out'
            }
        }
        
        stage('Terraform Apply') {
            steps {
                input message: 'Approve the plan?', ok: 'Apply'
                sh 'terraform apply tfplan'
            }
        }
        
        stage('Terraform Destroy') {
            steps {
                input message: 'Do you want to destroy the infrastructure?', ok: 'Destroy'
                sh 'terraform destroy -auto-approve'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
```

### Step 5: Add Terraform Scripts

1. **Create a Repository**
   - Create a Git repository (e.g., GitHub, GitLab) to store your Terraform scripts.

2. **Add Terraform Files**
   - Add your Terraform configuration files (e.g., `main.tf`, `variables.tf`, `outputs.tf`) to the repository.

#### Step 6. Trigger the Pipeline

- Commit and push your `Jenkinsfile` and Terraform code to your Git repository.
- Jenkins will automatically detect the change (if configured for polling) and trigger the pipeline.

OR 

### Step 6: Execute the Pipeline

1. **Run the Pipeline**
   - Go to the Jenkins dashboard, select your pipeline job, and click "Build Now."
   - Monitor the job's progress in the build console output.
  
### Explanation

1. **Agent**: Specifies that any available Jenkins agent can run the pipeline; **agent any**: Runs the pipeline on any available agent.

2. **Environment**: Sets environment variables, such as AWS credentials, using Jenkins credentials bindings.

3. **Stages**: Defines the stages of the pipeline:
   - `Clone repository`: Clones the Git repository containing the Terraform scripts.
   - `Terraform Init`: Initializes the Terraform working directory.
   - `Terraform Plan`: Generates and shows an execution plan.
   - `Terraform Apply`: Applies the changes required to reach the desired state, with a manual approval step.
   - `Terraform Destroy`: Optionally destroys the infrastructure if approved.

4. **Post Actions**: `always` cleans the workspace after the job completes, ensuring no residual files affect subsequent builds.
   **post**: Defines actions to take after the pipeline execution. `always` ensures cleanup regardless of the build result.
   
This setup provides a comprehensive CI/CD pipeline for managing infrastructure with Terraform in Jenkins. Adjust the configuration based on your specific requirements, such as adding more stages, handling different environments, or integrating additional tools.
By following these steps, you should have a robust and error-free Jenkins pipeline for deploying infrastructure using Terraform.

### Additional Tips

- **State Management**: Ensure proper management of the Terraform state file. Use remote backends like S3 for AWS.
- **Secrets Management**: Use Jenkins credentials and environment variables to manage sensitive information.
- **Error Handling**: Implement error handling and notifications for pipeline failures.

