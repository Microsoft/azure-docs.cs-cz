---
title: Škálování aplikací Azure App Service web apps pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit webovou aplikaci s modulem runtime kontejnerů Java 8 a Tomcat ve službě App Service v Linuxu.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, Azure App Service, Web Apps, škálování, Java
author: tomarchermsft
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: a63289c3dec41217725199614420935532a04aa4
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051675"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Škálování aplikací Azure App Service web apps pomocí Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (nebo jenom Web Apps) hostitele webové aplikace, rozhraní REST API a mobilní back-endu. Můžete vyvíjet ve svém oblíbeném jazyce &mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP nebo Python.

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. V tomto článku se dozvíte, jak použít Ansible umožní škálování vaší aplikace ve službě Azure App Service.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service Web Apps** – Pokud ještě nemáte webová aplikace služby Azure app service, můžete si [vytvoření webové aplikace Azure pomocí Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Vertikální navýšení kapacity aplikace ve službě App Service
Je možné škálovat i změnou cenové úrovně plánu služby App Service, které vaše aplikace patří. Tato část nabízí ukázkové playbook Ansible, který definuje následující operace:
- Získat faktů existující plán služby App Service
- Aktualizovat plán služby App service na S2 tři pracovní procesy

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Uložit tento playbook jako *webapp_scaleup.yml*.

Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook webapp_scaleup.yml
```

Po spuštění playbooku, výstup podobný následující příklad ukazuje, že plán služby App service se úspěšně aktualizoval na S2 s tři pracovní procesy:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)