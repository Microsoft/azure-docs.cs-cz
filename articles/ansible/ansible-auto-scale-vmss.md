---
title: Kurz – automatické škálování škálovací sady virtuálních počítačů v Azure pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k škálování škálovací sady virtuálních počítačů s automatickým Škálováním v Azure
keywords: ansible, azure, devops, bash, playbook, škálování, automatické škálování, virtuálních počítačů, škálovací sadu virtuálních počítačů, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231279"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Kurz: Automatické škálování škálovací sady virtuálních počítačů v Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkce automaticky úpravy počtu instancí virtuálních počítačů se nazývá [automatického škálování](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Výhodou automatického škálování je, že snižuje režie na správu pro monitorování a optimalizace výkonu vaší aplikace. Automatické škálování je možné nakonfigurovat jako reakce na vyžádání nebo podle daného plánu. Pomocí Ansible, můžete zadat pravidla automatického škálování, které definují přijatelný výkon pro pozitivní zkušenosti.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definice profilu automatického škálování
> * Automatické škálování podle plánu opakování
> * Automatické škálování na základě výkonu aplikace
> * Načíst informace o nastavení automatického škálování 
> * Zakázání nastavení automatického škálování

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu

Pokud chcete povolit automatické škálování na škálovací sadě, je nejdříve potřeba definovat profil automatického škálování. Tento profil definuje výchozí, minimální a maximální kapacitu škálovací sady. Tato omezení umožňují řídit náklady tím, že vytvoříte ne průběžně instancí virtuálních počítačů a vyvážení přijatelný výkon s minimální počet instancí, které zůstávají v případě škálování na méně instancí. 

Ansible umožňuje škálování škálovací sady na konkrétní datum nebo opakovaně.

Playbook kód v této části zvýší počet instancí virtuálních počítačů na tři v každé pondělí 10:00.

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatické škálování podle data o výkonu

Hodnota se zvyšuje vaši aplikace vyžadují, nastaví zatížení v instancích virtuálních počítačů ve škálovací zvyšuje. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Ansible umožňuje řídit, které metriky pro monitorování, jako je například využití procesoru, využití disku a doba načtení aplikace. Je možné škálovat v a horizontální navýšení kapacity škálovací nastaví na základě výkonu prahové hodnoty metrik, podle opakovaného plánu nebo podle konkrétního data. 

Playbook kódu v této části pracovního vytížení procesoru vyhledá předchozí 10 minut v každé pondělí 18:00. 

Podle metrik procento využití procesoru, provede playbook jednu z následujících akcí:

- Škáluje počet instancí virtuálních počítačů na čtyři
- Škálování počtu instancí virtuálních počítačů na jeden

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Získejte informace o nastavení automatického škálování 

Playbook kód v této části používá `azure_rm_autoscale_facts` modul načíst podrobnosti o nastavení automatického škálování.

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Zakázat nastavením automatického škálování

Existují dva způsoby, jak zakázat nastavením automatického škálování. Jedním ze způsobů je změnit `enabled` klíče z `true` k `false`. Druhý způsob je odstranit nastavení.

Playbook kódu v této části odstraní zadané nastavení automatického škálování. 

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Kurz: Nastaví vlastní obrázek aktualizace sady škálování virtuálních počítačů Azure pomocí Ansible](./ansible-vmss-update-image.md)