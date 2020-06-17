# microservice-pipeline
Setting up a shared library and seed job in Jenkins

We will set up a Freestyle project (seedJob) that will be a job that creates jobs. We will use the Jenkins Job DSL API to set up the foundation for supporting onboarding any number of services.

# Goals
1. Set up a Jenkins Freestyle Job called seedJob to create and configure other jobs using the Jenkins Job DSL API.
2. Store the seedJob configuration in github.
3. Run the seedJob to create a Freestyle Job called poc-micro.

# Install the Job DSL plugin
Navigate to Jenkins console in your browser and login to Jenkins with the credentials you set up (or the default admin ones provided to you during the cluster set up). We need to configure Jenkins to use the Jenkins Job DSL API. This provides us the functionality to configure how we want our new jobs built.

1. Navigate to Dashboard > Manage Jenkins > Manage Plugins > select Available tab > search for Job DSL and install.

# Creating the Freestyle Project seedJob
We will set up our seedJob the same way any other job is created.

1. On the left hand side of the page, select New Item
2. In the text box for Enter an item name, enter seedJob > select the Freestyle project > select OK.

![alt text](https://raw.githubusercontent.com/ippontech/blog-usa/master/images/2018/05/jenkins-shared-library-1.1.png?raw=true)

# Configure seedJob to use microservice-pipelines github repository
We are going to store the code for our seedJob in a github repository called microservice-pipelines. Since we are using the microservice-pipelines repository to load seed.groovy, we need to configure seedJob to use this repository.

1. Navigate to Dashboard > select seedJob > select Configure.
2. Scroll to the Source Code Management section > select Git.
3. In the Repository URL field, enter https://github.com/jitendar-singh/microservice-pipelines.
Leave everything else as the default configuration.

# Configure seedJob to use the seed.groovy file we will store in github
Now that we have configured Jenkins to use our microservice-pipelines repository, we need to set up seedJob to load seed.groovy from our microservice-pipelines repository. This is necessary for us to start using the Jenkins Job DSL API functionality.

* Note: This is not a requirement. Directly inside of the seedJob, you could add a groovy script to do the same thing we are doing in our microservice-pipelines repository (without the benefit of version control).
Since we will be using our microservice-pipelines repository, we will need to add some additional configuration to the seedJob to get this working.

1. Navigate to Dashboard > select seedJob > select Configure.
2. Scroll to the Build section > select Add Build step > Select Process Job DSLs.
3. Select Look on Filesystem.
4. In the DSL Scripts input field, enter dsl/seed.groovy (this is the path to the seed.groovy file we will be setting up later).
Leave everything else as the default configuration.

# Configure seedJob to use the microservice name as the job name
We will give our job the name of the microservice we plan to build (poc-micro). In order to do this we will need to add a String parameter to the seedJob that will be used inside of seed.groovy.

1. Navigate to Dashboard > select seedJob > select Configure .
2. Select This project is parameterized > select Add Parameter > select String Parameter.
3. Enter jobName in Name field.
4. In the Description field, enter The name of your repo (e.g. poc-micro).

# Goal 2
We are using the microservice-pipelines github repository. This repository will be used to store our seed code. In Part 2 of this series, we will include our Shared Library code in this repository.

1. Inside of the microservice-pipelines repository, we have created a directory dsl with seed.groovy inside.
* microservice-pipelines/dsl/seed.groovy.
2. Below are the contents of seed.groovy.
* We create a simple Freestyle Job and use the String Param named jobName from seedJob to name our Freestyle job.

# Goal 3
Now that we have our seedJob setup to read in seed.groovy from our github microservice-pipelines repository, we are ready to trigger our seedJob to create a Freestyle job with jobName.

1. Running the seedJob
2. Navigate to Dashboard > select seedJob > select Build Now.
3. Under Build History, select the top red circle.
4. This will take you to the Console Output.
* The job failed.
* Due to Script Security, this will happen every time you change seed.groovy. The Script Security Plugin is integrated with the Job DSL plugin and the Script Security Plugin is set up with a set of scripts that are pre approved for use. Since this is a new script, it will require an admin approval to use.

# Approving our seed.groovy script
1. We need to tell Jenkins it is ok to use this script.
* Navigate to Dashboard > Manage Jenkins > In-process Script Approval.
* Select Approve for the seed.groovy script.

![alt text](https://raw.githubusercontent.com/ippontech/blog-usa/master/images/2018/05/jenkins-shared-library-seed-script-approval.png?raw=true)

# Rerunning the seedJob
Now that we have approved seed.groovy, we are ready for our seedJob to run (and succeed).

1. Navigate to Dashboard > select seedJob > select Build Now.
2. Under Build History, select the top blue circle.
3. Inside of Console Output, you will see GeneratedJob{name='freestyle'}.
* Jenkins has created a new job called poc-micro fromseed.groovy.

# Conclusion
Our seedJob onboards a very simple Freestyle Job (poc-micro) from our microservice-pipelines repository. The benefit of microservice-pipelines is that we are now maintaining all of our pipeline configurations in source control. Using seed jobs allows us to onboard/re-onboard services quickly and easily (history is maintained for re-onboarded jobs).
