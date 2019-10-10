---
title: Kurz – škálování aplikací v Azure App Service pomocí Ansible
description: Naučte se navýšení kapacity aplikace v Azure App Service
keywords: Ansible, Azure, DevOps, bash, PlayBook, Azure App Service, Webová aplikace, škálování, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 0adcddb8afa4f0e3d0b9288f351dae7d77922612
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241496"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Kurz: škálování aplikací v Azure App Service pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Získání faktů stávajícího plánu App Service
> * Horizontální navýšení kapacity App Service plánu na S2 a tři pracovní procesy

## <a name="prerequisites"></a>Požadované součásti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service aplikaci** – pokud nemáte Azure App Service aplikaci, [nakonfigurujte aplikaci v Azure App Service pomocí Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Horizontální navýšení kapacity aplikace

Existují dva pracovní postupy pro škálování: *horizontální navýšení kapacity a horizontální navýšení* kapacity.

**Horizontální navýšení kapacity:** Pro horizontální navýšení kapacity pro získání dalších prostředků. Tyto prostředky zahrnují procesor, paměť, místo na disku, virtuální počítače a další. Nahorizontální navýšení kapacity aplikace změnou cenové úrovně plánu App Service, do kterého aplikace patří. 
Horizontální navýšení **kapacity:** K horizontálnímu navýšení kapacity můžete zvýšit počet instancí virtuálních počítačů, na kterých běží vaše aplikace. V závislosti na cenové úrovni plánu App Service můžete horizontální navýšení kapacity na maximálně 20 instancí. Automatické [škálování](/azure/azure-monitor/platform/autoscale-get-started) umožňuje škálovat počet instancí automaticky na základě předdefinovaných pravidel a plánů.

Kód PlayBook v této části definuje následující operaci:

* Získání faktů stávajícího plánu App Service
* Aktualizace plánu služby App Service na S2 se třemi pracovními procesy

Následující PlayBook uložte jako `webapp_scaleup.yml`:

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)