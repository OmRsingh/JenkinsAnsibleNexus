Whole architecture step by step:
## 1. Setup Nexus Sonatype on Ec2 and create below on it:
### Maven Repositories

1. **Release Repository**  
   - Name: `vprofile-release`  
   - Type: Maven 2 Hosted  
   - Version: Release  
   - Purpose: Store final release artifacts ready for production deployment

2. **Snapshot Repository**  
   - Name: `vprofile-snapshot`  
   - Type: Maven 2 Hosted  
   - Version: Snapshot  
   - Purpose: Store in-progress builds and artifacts for feature branches / testing

3. **Proxy Repository**  
   - Name: `vpro-maven-central`  
   - Type: Maven 2 Proxy  
   - Version: Release  
   - Purpose: Cache external Maven dependencies locally to improve build reliability
   - Pull from remote repos to enable caching dependencies.

4. **Group Repo**  
   - Name: `vprofile-grp-repo`  
   - Type: Maven 2 group  
   - Version: Mixed  
   - Purpose: Club above 3 repo into it for manageability.

## 2. Setup Jenkins on ec2 and create below on it:
### Jenkins
1. **Adding JDK**
   - Configuration:
   - path: /usr/lib/jvm/java-17-openjdk-amd64
   - install via ssh: sudo apt install openjdk-17-jdk

2. **Adding MAVEN3**
   - Configuration:
   - Name: `MAVEN3` 
   - Purpose: Build runtime for jenkins server.

3. **Add plugins**
   - Nexus Artifact Uploader
   - SonarQube Scanner
   - Github
   - Maven Integration
   - Slack Notification
   - BUILD TIMESTAMP

4. **Setup Nexus creds for jenkins to be able to connect to nexus sonatype repository**
   - Credentails (Global): User and password with admin and 'your password' and description and ID as nexuslogin

5. **Add Sonarqube Creds for Jenkins to be able to publish to sonarqube ec2.**
   - Sonar Username and password
   - Generate Token on sonarqube and add to jenkins credentails.

6. **Pipeline**
   - Add nexus properties on environment stage.
   - Generate and add your public key to github.
   - and private key into ssh with username field when creatin pipeline.
   - If host key verification failed then ssh into jenkins server and do ssh -T git@github.com.

