## Pull the IRIS docker image (community edition)
## 
## https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=ACLOUD#ACLOUD_image
##
## The community edition has a license which expires 1 year after the release date, and other limitations
## like 8 cpus max, 10Gb max, etc. described here:
## https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=ACLOUD#ACLOUD_limits
##

docker pull containers.intersystems.com/intersystems/iris-community:2022.1.0.209.0

## Create and run a docker container from that image:
##
## The "--check-caps false" was added according to this:
## https://es.community.intersystems.com/post/uso-de-los-contenedores-de-intersystems-iris-con-docker-201014
##
## because otherwise failed to run.

docker run --name iris -d --publish 1972:1972 --publish 52773:52773 containers.intersystems.com/intersystems/iris-community:2022.1.0.209.0 --check-caps false

##
## Open the following URL with your browser:
##
## https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=ACLOUD#ACLOUD_portal
##
## using the default username "_SYSTEM" and password "SYS" (you'll be prompted to change them)
##

http://localhost:52773/csp/sys/UtilHome.csp

##
## We can configure to have users created, databases etc. for development purposes, using configuration files
## which are then merged with the configuration and actions defined in this location of the container '/usr/irissys/iris.cpf' 
##
##
## https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=ACMF
##
## 
## There isn't much information in the documentation on these [Actions], but the following github provides an example:
##
## https://github.com/intersystems-community/configuration-merge-file-2020.4/tree/master/3-Configuration-Databases_and_Namespaces
##

##
## The idea is to bind mount the configuration file in the docker iris container, and then create and run the container like this:
##

docker run -d \
  -p 1972:1972 \
  -p 52773:52773 \
  -v [ABS_PATH_TO]/iris_config/development_merge_config.cpf:/durable/development_merge_config.cpf \
  -h iris \
  --name iris \
  --cap-add IPC_LOCK \
  --env ISC_DATA_DIRECTORY=/durable/irisdata \
  --env ISC_CPF_MERGE_FILE=/durable/development_merge_config.cpf \
  containers.intersystems.com/intersystems/iris-community:2022.1.0.209.0 \
  --check-caps false


