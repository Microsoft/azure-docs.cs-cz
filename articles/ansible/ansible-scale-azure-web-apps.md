---
title: Kurz – škálování aplikace ve službě Azure App Service pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak škálovat aplikaci ve službě Azure App Service
keywords: ansible, azure, devops, bash, playbooku, Azure App Service, Web Apps, škálování, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 213c4e086db8b40fdec26ce9fb3e0be5ad055cbc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729427"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Kurz: Škálování aplikací ve službě Azure App Service pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Získat faktů existující plán služby App Service
> * Vertikální navýšení kapacity plánu služby App Service na S2 s tři pracovní procesy

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplikace Azure App Service** – Pokud nemáte aplikaci služby Azure App Service [aplikaci nakonfigurovat ve službě Azure App Service pomocí Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Vertikální navýšení kapacity aplikace

Existují dva pracovní postupy pro škálování: *vertikálně navýšit kapacitu* a *horizontální navýšení kapacity*.

**Vertikální navýšení kapacity:** Vertikální navýšení kapacity znamená, že chcete získat více prostředků. Tyto prostředky zahrnují procesor, paměti, místa na disku, virtuální počítače a další. Vertikálně navýšit kapacitu aplikace změnou cenové úrovně plánu služby App Service, ke kterému aplikace patří. 
**Horizontální navýšení kapacity:** Horizontální navýšení kapacity znamená zvýšit počet instancí virtuálních počítačů, na kterých běží vaše aplikace. V závislosti na vaší cenové úrovně plánu služby App Service můžete horizontální navýšení kapacity na až 20 instancí. [Automatické škálování](/azure/azure-monitor/platform/autoscale-get-started) umožňuje škálovat počet instancí automaticky na základě předdefinovaných pravidel a plány.

Playbook kód v této části definuje následující operace:

* Získat faktů existující plán služby App Service
* Aktualizovat plán služby App service na S2 tři pracovní procesy

Uložte následující ukázkový playbook jako `webapp_scaleup.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook webapp_scaleup.yml
```

Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)