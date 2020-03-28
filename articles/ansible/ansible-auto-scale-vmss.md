---
title: Kurz – škálovací sady virtuálních strojů v Azure pomocí automatického škálování pomocí
description: Naučte se používat Ansible k škálování škálovacích sad virtuálních strojů pomocí automatického škálování v Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156812"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: Škálovací sady virtuálních strojů v Azure s automatickým škálování pomocí ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkce automatické úpravy počtu instancí virtuálních virtuálních mit se nazývá [automatické škálování](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Výhodou automatického škálování je, že snižuje režii na správu pro monitorování a optimalizaci výkonu vaší aplikace. Automatické škálování lze nakonfigurovat v reakci na poptávku nebo podle definovaného plánu. Pomocí Ansible můžete určit pravidla automatického škálování, která definují přijatelný výkon pro pozitivní zákaznickou zkušenost.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definice profilu automatického škálování
> * Automatické škálování na základě opakovaného plánu
> * Automatické škálování na základě výkonu aplikace
> * Načíst informace o nastavení automatického škálování 
> * Zakázání nastavení automatického škálování

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování na základě plánu

Pokud chcete povolit automatické škálování na škálovací sadě, je nejdříve potřeba definovat profil automatického škálování. Tento profil definuje výchozí, minimální a maximální kapacitu škálovací sady. Tato omezení umožňují řídit náklady tím, že neustále nevytváříte instance virtuálních ms, a vyvažují přijatelný výkon s minimálním počtem instancí, které zůstávají v události škálování. 

Ansible umožňuje škálovat sady měřítek v určitém datu nebo opakovaném plánu.

Kód playbooku v této části zvyšuje počet instancí virtuálních her na tři v 10:00 každé pondělí.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatické škálování na základě údajů o výkonu

Pokud se zvýší požadavek na vaši aplikaci, zvýší se zatížení instancí virtuálních aplikací ve škálovacích sadách. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Ansible umožňuje řídit, jaké metriky sledovat, jako je využití procesoru, využití disku a doba načítání aplikací. Škálování a škálování v škálovacích sadách na základě prahových hodnot metrik výkonu, podle opakovaného plánu nebo podle konkrétního data. 

Playbook kód v této části kontroluje zatížení procesoru za předchozích 10 minut v 18:00 každé pondělí. 

Na základě metrik procenta procesoru playbook provádí jednu z následujících akcí:

- Škály na více než čtyř instancí virtuálních mís.
- Škály v počtu instancí virtuálních mís na jeden

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Získání informací o nastavení automatického škálování 

Kód playbooku v této `azure_rm_autoscale_facts` části používá modul k načtení podrobností nastavení automatického škálování.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Zakázání nastavení automatického škálování

Nastavení automatického škálování lze zakázat dvěma způsoby. Jedním ze způsobů `enabled` je `true` změna `false`klíče z na . Druhým způsobem je odstranění nastavení.

Kód playbooku v této části odstraní nastavení automatického škálování. 

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kurz: Aktualizace vlastní image škálovacích sad virtuálních strojů Azure pomocí ansible](./ansible-vmss-update-image.md)