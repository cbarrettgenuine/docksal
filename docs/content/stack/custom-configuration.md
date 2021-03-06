---
title: "Custom Configuration"
weight: 3
aliases:
  - /en/master/advanced/stack-config/
---
## Custom Configuration {#custom-configuration}

Custom configurations are useful when you have a larger or more complex project. One where a CI server is involved 
or many people are on a project team, and you have to be careful about maintaining software versions. 
Having a custom configuration will protect your project from the updates in `services.yml` when you update Docksal.

```bash
fin config generate
```

This command will create a `docksal.yml` file in the project directory. You can update this file with a fully
independent description of services so future changes to the default stack(s) will no longer affect the project 
configuration. This also means that future Docksal updates, bringing new features and changes, will not automatically 
apply. You might need to append those changes manually in `docksal.yml`.

### Don't Break Your Docksal Setup! List of Must Have Values {#warning}


{{% notice warning %}}
Certain configuration settings in yaml files are required for your Docksal stack to function properly.
{{% /notice %}}

#### web

In the `web` service, there are settings defined in the `volumes`, `labels`, `environment`, and `depends_on` sections. 
You should not remove or change these values.

```yaml
  web:
    volumes:
      # Project root volume
      - project_root:/var/www:ro,nocopy
    labels:
      - io.docksal.virtual-host=${VIRTUAL_HOST},*.${VIRTUAL_HOST},${VIRTUAL_HOST}.*
      - io.docksal.cert-name=${VIRTUAL_HOST_CERT_NAME:-none}
      - io.docksal.project-root=${PROJECT_ROOT}
      - io.docksal.permanent=${SANDBOX_PERMANENT:-false}
    environment:
      - APACHE_DOCUMENTROOT=/var/www/${DOCROOT:-docroot}
    # cli has to be up before web
    depends_on:
      - cli
```

#### cli

In the `cli` service, there is the `volumes` section. You should not remove or change these volumes.

```yaml
  cli:
    volumes:
      # Project root volume
      - project_root:/var/www:rw,nocopy
      # Shared ssh-agent socket
      - docksal_ssh_agent:/.ssh-agent:ro
```