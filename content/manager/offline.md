---
layout: bt_wiki
title: Offline manager
category: Blueprints
draft: false
weight: 11000

---
Deploying an offline manager is easy using Cloudify. Cloudify manager consists of the manager source code in addition to external components, plugins, modules and agent packages. Creating a file server reachable from the designated managers network and configuring these paths in the inputs file is all the configuring you need.

- **Core resources** - Some of the resources needed are a part of the manager's core. Without them, different manager modules will simply fail to run. You can find these under the Cloudify Modules and External Components section. This resources are absolutely necessary for the Cloudify manager to run correctly.
- **Agent packages** - The agent packages are build on a per distro basis. provided with centos7, centos 6.5, Ubuntu trusy, Ubuntu Precise, RHEL and windows, these enable the Cloudify agent to run on different distro.
- **Deployment resources** - This sections differs from the others, by functionality. Some blueprints require a resource via url, since you would work in an offline environment, this resource is missing.

In order to resolve this issue, Cloudify manager starts up a file server on port 53229 which would hold any resource needed by any blueprint.
Cloudify manager blueprint provides a simple api for uploading resources, and resolving resources paths:

### Uploading resources
Cloudify provides you with an easy way for uploading resources to the manager. In the following example you can see that an `upload_resource` section is comprised out of two 
resource type: 
 
#### Dsl resources
The `dsl_resources` section enables you to upload any resource needed for the parsing of blueprints to the manager.
#### Plugin resources
This `plugin_resources` uses the new [plugins]({{< relref "plugins/overview/" >}}) api. Any path passed to this section
is passed as a plugin to upload to the new plugins api. Further more, any remote url passed to this section
is first downloaded to the local machine, and then uploaded via the plugins api.

{{< gsHighlight  yaml  >}}
 upload_resources:
   plugin_resources: 
     - 'http://www.my-plugin.com/path/to/plugin.wgn'
   dsl_resources: 
     - 'source_path': 'http://www.my-plugin.com/path/to/plugin.yaml'
       'destination_path': '/path/to/local/plugin.yaml'
{{< /gsHighlight >}}

### Resolving inputs

Cloudify uses the [Import resolver]({{< relref "blueprints/import-resolver.md" >}}) section in the manager blueprint, in order to translate any resource appears in the import section of the blueprint to a custom translation specified by the section. 
For example, an `import_resolver` section might look something like this: 
 {{< gsHighlight  yaml  >}}
 import_resolver:
   parameters:
     rules:
     - {'http://www.my-plugin.com/path': 'http://localhost:53229/path'}
 {{< /gsHighlight >}}
The rule basically specifies a mapping between the source address ('http://www.my-plugin.com/path') as the dictionary key, and the destination address ('http://localhost:53229/path') as the dictionary value.
Once the manager parses the blueprint's `import` section, it first tries the new value supplied, if it fails it falls back to the original address. 
For example, lets have a look on an import example:

{{< gsHighlight  yaml  >}}
imports:
  - http://www.my-plugin.com/path/to/plugin.yaml
{{< /gsHighlight >}}

Upon parsing the blueprint file, the manager tries to resolve each and every resource in the imports list according to the specified rules. In our example, the 
url http://www.my-plugin.com/path/to/plugin.yaml would be first mapped to http://localhost:53229/path/to/local/plugin.yaml, and the manager would try to retrieve 
the resource from the resolved url. If that fails, the manager would fall back to the original url.
