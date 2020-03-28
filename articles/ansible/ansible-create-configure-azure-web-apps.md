---
title: Kurz – Konfigurace aplikací ve službě Azure App Service pomocí Ansible
description: Přečtěte si, jak vytvořit aplikaci ve službě Azure App Service s Javou 8 a runtime kontejneru Tomcat
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156571"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Kurz: Konfigurace aplikací ve službě Azure App Service pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření aplikace ve službě Azure App Service s Javou 8 a runtime kontejneru Tomcat
> * Vytvoření profilu Azure Traffic Manageru
> * Definování koncového bodu Traffic Manageru pomocí vytvořené aplikace

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Vytvoření základní služby aplikace

Kód playbooku v této části definuje následující zdroje:

* Skupina prostředků Azure, ve které se nasazují plán služby App Service a aplikace
* Služba aplikací na Linuxu s Java 8 a runtime kontejneru Tomcat

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook firstwebapp.yml
```

Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Vytvoření aplikace a použití Azure Traffic Manageru

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) umožňuje řídit, jak se požadavky od webových klientů distribuují do aplikací ve službě Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, Traffic Manager bude sledovat stav vašich aplikací App Service. Mezi stavy patří Spuštěno, Zastaveno nebo Odstraněno. Traffic Manager se používá k rozhodnutí, které koncové body by měly přijímat přenosy.

Ve službě App Service se aplikace spouští v rámci [plánu služby App Service](/azure/app-service/overview-hosting-plans). Plán služby App Service definuje sadu výpočetních prostředků pro spuštění aplikace. Plán služby App Service a webovou aplikaci můžete spravovat v různých skupinách.

Kód playbooku v této části definuje následující zdroje:

* Skupina prostředků Azure, ve které se nasadí plán služby App Service
* Plán služby App Service
* Skupina prostředků Azure, ve které se aplikace nasazuje
* Služba aplikací na Linuxu s Java 8 a runtime kontejneru Tomcat
* Profil služby Traffic Manager
* Koncový bod Traffic Manageru pomocí vytvořené aplikace

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook webapp.yml
```

Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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
> [Kurz: Škálování aplikací ve službě Azure App Service pomocí Ansible](/azure/ansible/ansible-scale-azure-web-apps)