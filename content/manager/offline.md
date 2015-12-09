---
layout: bt_wiki
title: Advanced Blueprint Example
category: Blueprints
draft: false
weight: 11000

---
# Offline Manager
Deploying an offline manager is easy using cloyudify. Cloudify manager consists of the manager source code in addition to external components, plugins, modules and agent packages. Creating a file server reachable from the designated managers network and configuring these paths in the inputs file is all the configuring you need.

## The resources
Frist let us go through the resources and why we actually need them. 
### Core resources
Some of the resources needed are a part of the manager's core. Without them, different manager modules will simply fail to run. You can find these under the Cloudify Modules and External Componenets section. This resources are absolutley necessary for the cloudify manager to run correctly.
### Agent packages 
The agent packages are build on a per distro basis. provided with centos7, centos 6.5, Ubuntu trusy, Ubuntu Precise, RHEL and windows, these enable the cloudify agent to run on different distro.
### Upload resources
This sections differes from the others, by functionality. Any resource speicified in this section, is used for the deployments in the deployments process. For example, the hello_world_example imports the following:
  - http://www.getcloudify.org/spec/cloudify/3.3/types.yaml
  - http://www.getcloudify.org/spec/openstack-plugin/1.3/plugin.yaml
  - http://www.getcloudify.org/spec/diamond-plugin/1.3/plugin.yaml

each import is of yaml type. this enables the parsing of the hello_world blueprint. However, in an offline system these urls are not reachable. To solve this issue, the manager itself actually runs a file server on port 53229. 
For additional info, please refer to the Upload_resources.
#### dsl_resources
The dsl_resources section enables you to upload any resource needed for the parsing of blueprints to the manager.
#### plugin_resources
This section actually holds the code for the plugin itself (in wagon type).

# Bootstraping
Once the file server is up and running, all you left to do is decomment any resource related lines in the inputs file, and change the url from the default url, to an url to the file server you've just populated.

Once both stages are done, you would have a fully functioning cloudify manager. 