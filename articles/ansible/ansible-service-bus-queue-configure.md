---
title: Kurz – konfigurace front v Azure Service Bus pomocí Ansible
description: Naučte se, jak pomocí Ansible vytvořit frontu Azure Service Bus.
keywords: Ansible, Azure, DevOps, bash, PlayBook, Service Bus, Queue
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a48796c2177a8b5b818553bf8aa0ff36f712d4e0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241385"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Kurz: konfigurace front v Azure Service Bus pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Vytvoření plicy SAS
> * Načíst informace oboru názvů
> * Načíst informace o frontě
> * Odvolat zásady SAS pro frontu

## <a name="prerequisites"></a>Požadované součásti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Vytvoření fronty Service Bus

Vzorový PlayBook kód vytvoří následující prostředky:
- Skupina prostředků Azure
- Obor názvů Service Bus v rámci skupiny prostředků
- Service Bus frontu s oborem názvů

Následující PlayBook uložte jako `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Vytvoření zásad SAS

[Sdílený přístupový podpis (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) je ověřovací mechanismus založený na deklaracích, který používá tokeny. 

Vzorový kód PlayBook vytvoří dvě zásady SAS pro Service Bus frontu s různými oprávněními.

Následující PlayBook uložte jako `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Před spuštěním PlayBook se podívejte na následující poznámky:
- Hodnota `rights` představuje oprávnění, které má uživatel s frontou. Zadejte jednu z následujících hodnot: `manage`, `listen`, `send` nebo `listen_send`.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Načíst informace oboru názvů

Vzorový kód PlayBook se dotazuje na informace oboru názvů.

Následující PlayBook uložte jako `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Před spuštěním PlayBook se podívejte na následující poznámky:
- Hodnota `show_sas_policies` označuje, zda se mají v rámci zadaného oboru názvů zobrazovat zásady SAS. Ve výchozím nastavení je hodnota `False`, aby se předešlo dalšímu síťovému zatížení.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Načíst informace o frontě

Ukázkový PlayBook kód dotazuje informace o frontě. 

Následující PlayBook uložte jako `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Před spuštěním PlayBook se podívejte na následující poznámky:
- Hodnota `show_sas_policies` označuje, zda se mají v zadané frontě zobrazovat zásady SAS. Ve výchozím nastavení je tato hodnota nastavená na `False`, aby se předešlo dalšímu síťovému zatížení.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odvolat zásady SAS pro frontu

Vzorový kód PlayBook odstraní zásady SAS pro frontu.

Následující PlayBook uložte jako `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Následující kód uložte jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Kurz: konfigurace tématu v Azure Service Bus pomocí Ansible](ansible-service-bus-topic-configure.md)