---
title: Kurz – automatické škálování virtuálních počítačů v Azure pomocí Ansible
description: Naučte se používat Ansible ke škálování služby Virtual Machine Scale Sets pomocí automatického škálování v Azure.
keywords: Ansible, Azure, DevOps, bash, PlayBook, škálování, automatické škálování, virtuální počítač, sada škálování virtuálních počítačů, VMSS
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156812"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: automatické škálování virtuálních počítačů ve službě Virtual Machine Scale Sets v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkce automatického přizpůsobení počtu instancí virtuálních počítačů se nazývá automatické [škálování](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Výhodou automatického škálování je, že snižuje režijní náklady na správu při monitorování a optimalizaci výkonu aplikace. Automatické škálování je možné nakonfigurovat v reakci na vyžádání nebo podle definovaného plánu. Pomocí Ansible můžete zadat pravidla automatického škálování, která definují přijatelný výkon pro pozitivní prostředí zákazníka.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definice profilu automatického škálování
> * Automatické škálování na základě plánu opakování
> * Automatické škálování na základě výkonu aplikace
> * Načíst informace o nastavení automatického škálování 
> * Zakázat nastavení automatického škálování

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu

Pokud chcete povolit automatické škálování na škálovací sadě, je nejdříve potřeba definovat profil automatického škálování. Tento profil definuje výchozí, minimální a maximální kapacitu škálovací sady. Tato omezení umožňují řídit náklady tím, že nedojde k neustálému vytváření instancí virtuálních počítačů a k vyvážení přijatelného výkonu s minimálním počtem instancí, které zůstávají v rámci události škálování na úrovni. 

Ansible umožňuje škálovat sady škálování podle konkrétního data nebo opakovaného plánu.

PlayBook kód v této části zvyšuje počet instancí virtuálních počítačů na tři v 10:00 každé pondělí.

Uložte následující ukázkový playbook jako `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatické škálování na základě údajů o výkonu

Pokud se vaše poptávka s aplikacemi zvýší, zatížení instancí virtuálních počítačů ve vašich sadách škálování se zvýší. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Ansible vám umožňuje řídit, které metriky se mají monitorovat, jako je využití CPU, využití disku a doba načítání aplikací. Můžete škálovat a škálovat sady škálování na základě prahových hodnot metriky výkonu, podle plánu opakování nebo podle konkrétního data. 

PlayBook kód v této části kontroluje úlohy procesoru za posledních 10 minut v 18:00 každé pondělí. 

V závislosti na procentuálních metrikách CPU PlayBook provede jednu z následujících akcí:

- Škálujte počet instancí virtuálních počítačů na čtyři.
- Škáluje počet instancí virtuálních počítačů na jeden.

Uložte následující ukázkový playbook jako `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Získat informace o nastavení automatického škálování 

Kód PlayBook v této části používá modul `azure_rm_autoscale_facts` k načtení podrobností nastavení automatického škálování.

Uložte následující ukázkový playbook jako `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Zakázat nastavení automatického škálování

Existují dva způsoby, jak zakázat nastavení automatického škálování. Jedním ze způsobů, jak `enabled` klíč změnit z `true` na `false`. Druhým způsobem je odstranit nastavení.

Kód PlayBook v tomto oddílu odstraní nastavení automatického škálování. 

Uložte následující ukázkový playbook jako `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kurz: aktualizace vlastní image služby Azure Virtual Machine Scale Sets pomocí Ansible](./ansible-vmss-update-image.md)