---
title: Kurz – škálování aplikací ve službě Azure App Service pomocí Ansible
description: Přečtěte si, jak vertikálně navýšit kapacitu aplikace ve službě Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155909"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Kurz: Škálování aplikací ve službě Azure App Service pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Získání faktů o existujícím plánu služby App Service
> * Škálování na výši plánu služby App Service na S2 se třemi pracovníky

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplikace Azure App Service** – Pokud nemáte aplikaci Azure App Service, [nakonfigurujte aplikaci ve službě Azure App Service pomocí Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Škálování navýšit kapacitu aplikace

Existují dva pracovní postupy pro škálování: *vertikálně navýšit kapacitu* a *horizontální navýšení kapacity*.

**Zvětšete kapacitu:** Chcete-li vertikálně navýšit prostředky získat více zdrojů. Tyto prostředky zahrnují procesor, paměť, místo na disku, virtuální počítače a další. Navýšit kapacitu aplikace změnou cenové úrovně plánu služby App Service, do kterého aplikace patří. 
**Horizontální navýšení kapacity:** Horizontální navýšení kapacity znamená zvýšit počet instancí virtuálních aplikací, které spouštějí vaši aplikaci. V závislosti na cenové úrovni plánu služby App Service můžete škálovat až na 20 instancí. [Automatické škálování](/azure/azure-monitor/platform/autoscale-get-started) umožňuje automaticky škálovat počet instancí na základě předdefinovaných pravidel a plánů.

Kód playbooku v této části definuje následující operace:

* Získání faktů o existujícím plánu služby App Service
* Aktualizace plánu služby App na S2 se třemi pracovníky

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook webapp_scaleup.yml
```

Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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