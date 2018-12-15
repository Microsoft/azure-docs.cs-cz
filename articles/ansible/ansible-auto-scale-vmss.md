---
title: Automatické škálování virtuálního počítače škálovací sady v Azure pomocí Ansible
description: Zjistěte, jak použít Ansible k škálování virtuálního počítače škálovací sady s automatickým Škálováním v Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, škálování, automatické škálování, virtuálních počítačů, škálovací sadu virtuálních počítačů, vmss
author: tomarcher
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: c6678d6df3a695d3a0471e5779bc3af4b6ba6c84
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410909"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Automatické škálování virtuálního počítače škálovací sady v Azure pomocí Ansible
Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Pomocí Ansible můžete spravovat škálovací sadu virtuálních počítačů v Azure stejně jako jakékoli jiné prostředky Azure. 

Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu. V tomto článku vytvoříte nastavení automatického škálování a přidružte ji k existující škálovací sady virtuálních počítačů. V nastavení automatického škálování můžete nakonfigurovat pravidlo pro horizontální navýšení kapacity nebo horizontálně, jak chcete.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Existující škálovací sady virtuálních počítačů Azure. – Pokud nemáte některou, [vytvoření škálovací sady virtuálních počítačů v Azure pomocí Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.7. 

## <a name="auto-scale-based-on-a-schedule"></a>Automatické škálování podle plánu   
Pokud chcete povolit automatické škálování na škálovací sadě, je nejdříve potřeba definovat profil automatického škálování. Tento profil definuje výchozí, minimální a maximální kapacitu škálovací sady. Tato omezení umožňují řídit náklady tím, že nedochází k nepřetržitému vytváření instancí virtuálních počítačů, a vyvažují přijatelný výkon s minimálním počtem instancí, které zůstanou v události horizontálního snížení kapacity. 

Můžete škálovat v a škálování na více instancí ve Škálovací sady virtuálních počítačů podle opakovaného plánu nebo podle konkrétního data. Tato část představuje playbook Ansible vzorku, který vytvoří nastavení automatického škálování, které zvýší počet instancí virtuálních počítačů na tři ve vaší škálovací sady na 10:00 každé pondělí tichomořském časovém pásmu. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

Uložit tento playbook jako *vmss. automatické scale.yml*. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Automatické škálování na základě dat výkonu
Hodnota se zvyšuje vaši aplikace vyžadují, nastaví zatížení v instancích virtuálních počítačů ve škálovací zvyšuje. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

Můžete škálovat v a škálování na více instancí ve Škálovací sady virtuálních počítačů na základě výkonu metriky prahových hodnot, podle opakovaného plánu nebo podle konkrétního data. Tato část nabízí ukázkové playbook Ansible, který kontroluje úlohy za posledních 10 minut na 18:00 každé pondělí, tichomořské časové pásmo a škálovat počet instancí virtuálních počítačů ve škálovací sadě na čtyři nebo škáluje na jednu instanci podle procentuální využití procesoru v metriky. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

Uložit tento playbook jako *vmss automatické škálování metrics.yml*. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Získejte informace pro stávající nastavení automatického škálování
Můžete získat podrobnosti o všech nastavení automatického škálování prostřednictvím *azure_rm_autoscale_facts* modul s playbook následujícím způsobem:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Zakázat nastavením automatického škálování
Zadané nastavení automatického škálování můžete zakázat tak, že změníte `enabled: true` k `enabled: false`, nebo odstranění nastavení automatického škálování pomocí playbooku následujícím způsobem:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Nastaví playbook Ansible vzorku pro škálování virtuálních počítačů](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)