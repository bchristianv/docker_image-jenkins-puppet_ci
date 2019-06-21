
# Setting up Jenkins on Docker for Puppet CI

#### Reference: https://github.com/jenkinsci/docker/blob/master/README.md

The version(s) of ruby to be pre-installed on the image can be changed by adding or removing versions in the `./src/ruby-build/` directory.

_See https://github.com/rbenv/ruby-build, specifically `./share/ruby-build/*`, for information on how to configure additional versions of ruby._

Additional Jenkins plugins can be pre-installed on the image by appending them to the plugins.txt file.

## Build a new Jenkins Puppet CI image from the Dockerfile

`docker build -t jenkins-puppet_ci:yyyy.mm.i .`

## Tag the newly created Jenkins Puppet CI image as `lts`
`docker tag jenkins-puppet_ci:yyyy.mm.i jenkins-puppet_ci:lts`

## Deploy a Jenkins Puppet CI container from the Jenkins Puppet CI image
```
docker run -d --name jenkins-puppet_cilts \
  -v jenkins-puppet_cilts-jenkins_home:/var/jenkins_home \
  -p 8080:8080 -p 50000:50000 \
  jenkins-puppet_ci:lts
```

## Get the admin password from the Jenkins Puppet CI container and log into the web interface to complete setup.
As of June 2019 it's possible to pre-install Jenkins plugins at the time of image build. However, it is NOT possible to skip ONLY the plugin selection part of the setup wizard when doing so, which is something one would think should be an available option.

For now rather than re-implement the setup wizard functionality, minus the plugin selection step, on initial Jenkins Puppet CI container deployment, go through the full setup wizard deselecting all of the plugins via the `All` link since they are already pre-installed.
 
See https://github.com/jenkinsci/docker/issues/310 for more details and possible work around solutions.

`docker exec -it jenkins-puppet_cilts cat /var/jenkins_home/secrets/initialAdminPassword`

## Notes:
### Deploying a Jenkins Docker container using the jenkins/jenkins:lts image
```
docker run -d --name jenkinslts \
  -v jenkinslts-jenkins_home:/var/jenkins_home \
  -p 8080:8080 -p 50000:50000 \
  jenkins/jenkins:lts
```

### Getting the list of plugins already installed on an existing Jenkins server
```
USERNAME=admin
PASSWORD=jenkins
JENKINS_HOST=localhost.localdomain.local
JENKINS_PORT=8080
JENKINS_BASEURL=${USERNAME}:${PASSWORD}@${JENKINS_HOST}:${JENKINS_PORT}
curl -sSL "http://${JENKINS_BASEURL}/pluginManager/api/xml?depth=1&xpath=/*/*/shortName|/*/*/version&wrapper=plugins" | \
  perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g' | \
  sed 's/ /:/' \
  >> plugins.txt
```


#### ToDo:
- Address the Jenkins setup wizard issue with plugin selection step
- Look into adding Puppet CI/CD pipeline configuration(s) to the Docker image
  - see puppet-ci.txt

####_to be continued..._
