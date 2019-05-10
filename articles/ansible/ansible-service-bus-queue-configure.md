---
title: Kurz – konfigurace front v Azure Service Bus pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytvoření fronty služby Azure Service Bus
keywords: ansible v azure, devops, bash, playbook, služby Service bus, front
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 6efc11106fae18beac43ab1896733ab6bfc64dad
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230767"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Kurz: Konfigurace front v Azure Service Bus pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Vytvoření SAS plicy
> * Načíst informace o oboru názvů
> * Načtení informací o frontě
> * Odvolat fronty zásady SAS.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Vytvořit frontu služby Service Bus

Playbook vzorový kód vytvoří následující prostředky:
- Skupina prostředků Azure
- Obor názvů služby Service Bus v rámci skupiny prostředků
- Fronty služby Service Bus s oborem názvů

Uložte následující ukázkový playbook jako `servicebus_queue.yml`:

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Vytvořit zásady SAS.

A [sdíleného přístupového podpisu (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) virtuálních sítí je mechanismus ověřování na základě deklarací identity pomocí tokenů. 

Playbook vzorový kód vytvoří dvě zásady SAS pro frontu služby Service Bus pomocí jiné oprávnění.

Uložte následující ukázkový playbook jako `servicebus_queue_policy.yml`:

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

Před spuštěním playbooku, viz následující poznámky:
- `rights` Hodnota představuje oprávnění má uživatel s frontou. Zadejte jednu z následujících hodnot: `manage`, `listen`, `send`, nebo `listen_send`.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Načíst informace o oboru názvů

Vzorový kód playbook zadá dotaz na informace oboru názvů.

Uložte následující ukázkový playbook jako `servicebus_namespace_info.yml`:

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

Před spuštěním playbooku, viz následující poznámky:
- `show_sas_policies` Hodnota označuje, jestli se má zobrazit zásady SAS v rámci zadaného oboru názvů. Výchozí hodnota je `False` aby se zabránilo další síťové zatížení.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Načtení informací o frontě

Ukázkové dotazy kód playbook fronty informace. 

Uložte následující ukázkový playbook jako `servicebus_queue_info.yml`:

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

Před spuštěním playbooku, viz následující poznámky:
- `show_sas_policies` Hodnota označuje, jestli se má zobrazit zásady SAS pod zadanou frontu. Ve výchozím nastavení, tato hodnota nastavená na `False` aby se zabránilo další síťové zatížení.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odvolat fronty zásady SAS.

Vzorový kód playbook odstraní zásady SAS fronty.

Uložte následující ukázkový playbook jako `servicebus_queue_policy_delete.yml`:

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

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

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Kurz: Konfigurace téma v Azure Service Bus pomocí Ansible](ansible-service-bus-topic-configure.md)