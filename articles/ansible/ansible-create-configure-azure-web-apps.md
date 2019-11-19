---
title: Kurz – konfigurace aplikací v Azure App Service pomocí Ansible
description: Naučte se, jak vytvořit aplikaci v Azure App Service pomocí Java 8 a modulu runtime kontejneru Tomcat
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156571"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Kurz: Konfigurace aplikací v Azure App Service pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření aplikace v Azure App Service pomocí Java 8 a modulu runtime kontejneru Tomcat
> * Vytvoření profilu Azure Traffic Manager
> * Definování koncového bodu Traffic Manager pomocí vytvořené aplikace

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Vytvoření základní aplikační služby

PlayBook kód v této části definuje následující zdroje:

* Skupina prostředků Azure, ve které se nasazují plán App Service a aplikace
* App Service v systému Linux s jazykem Java 8 a modulem runtime kontejneru Tomcat

Uložte následující ukázkový playbook jako `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook firstwebapp.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Vytvoření aplikace a použití Azure Traffic Manager

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) umožňuje řídit, jak se budou požadavky z webových klientů distribuovat do aplikací v Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, Traffic Manager bude sledovat stav vašich aplikací App Service. Mezi stavy patří Spuštěno, Zastaveno nebo Odstraněno. Traffic Manager slouží k rozhodnutí, které koncové body by měly přijímat přenosy.

Ve službě App Service se aplikace spouští v rámci [plánu služby App Service](/azure/app-service/overview-hosting-plans). Plán App Service definuje sadu výpočetních prostředků, které má aplikace běžet. Plán služby App Service a webovou aplikaci můžete spravovat v různých skupinách.

PlayBook kód v této části definuje následující zdroje:

* Skupina prostředků Azure, ve které je nasazený plán App Service
* Plán služby App Service
* Skupina prostředků Azure, do které se aplikace nasazuje
* App Service v systému Linux s jazykem Java 8 a modulem runtime kontejneru Tomcat
* Profil služby Traffic Manager
* Traffic Manager koncový bod využívající vytvořenou aplikaci

Uložte následující ukázkový playbook jako `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook webapp.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kurz: škálování aplikací v Azure App Service pomocí Ansible](/azure/ansible/ansible-scale-azure-web-apps)