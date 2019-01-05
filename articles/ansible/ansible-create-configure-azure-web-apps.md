---
title: Vytvoření webové aplikace Azure pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit webovou aplikaci s modulem runtime kontejnerů Java 8 a Tomcat ve službě App Service v Linuxu.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 106fe11355d629e57f71b12d1125d188b98c2236
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051341"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Vytvoření webové aplikace Azure App Service web apps pomocí Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (nebo jenom Web Apps) hostitele webové aplikace, rozhraní REST API a mobilní back-EndY. Můžete vyvíjet ve svém oblíbeném jazyce &mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP nebo Python.

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku se dozvíte, jak pomocí Ansible vytvořit webovou aplikaci s použitím modulu runtime Java. 

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.7.

## <a name="create-a-simple-app-service"></a>Vytvoření jednoduché služby App Service
Tato část představuje ukázkový playbook Ansible, který definuje následující prostředky:
- Skupina prostředků, do které se nasadí váš plán služby App Service a webová aplikace
- Webová aplikace s modulem runtime kontejnerů Java 8 a Tomcat ve službě App Service v Linuxu

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
Uložte předchozí playbook jako **firstwebapp.yml**.

Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook firstwebapp.yml
```

Výstup spuštění playbooku Ansible ukazuje úspěšné vytvoření webové aplikace:

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Vytvoření služby App Service pomocí služby Traffic Manager
Pomocí služby [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) můžete řídit způsob distribuce požadavků z webových klientů do aplikací ve službě Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, Traffic Manager bude sledovat stav vašich aplikací App Service. Mezi stavy patří Spuštěno, Zastaveno nebo Odstraněno. Traffic Manager se pak může rozhodnout, do kterého z těchto koncových bodů se má směrovat provoz.

Ve službě App Service se aplikace spouští v rámci [plánu služby App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
). Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace. Plán služby App Service a webovou aplikaci můžete spravovat v různých skupinách.

Tato část představuje ukázkový playbook Ansible, který definuje následující prostředky:
- Skupina prostředků, do které se nasadí váš plán služby App Service
- Plán služby App Service
- Sekundární skupina prostředků, do které se nasadí vaše webová aplikace
- Webová aplikace s modulem runtime kontejnerů Java 8 a Tomcat ve službě App Service v Linuxu
- Profil služby Traffic Manager
- Koncový bod služby Traffic Manager s využitím vytvořeného webu

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
Uložte předchozí playbook jako **webapp.yml** nebo si [playbook stáhněte](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook webapp.yml
```

Výstup spuštění playbooku Ansible ukazuje úspěšné vytvoření plánu služby App Service, webové aplikace a profilu a koncového bodu služby Traffic Manager:
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Škálování aplikací Azure App Service web apps pomocí Ansible](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)