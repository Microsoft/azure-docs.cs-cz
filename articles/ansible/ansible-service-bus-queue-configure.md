---
title: Kurz – konfigurace front v Azure Service Bus pomocí Ansible
description: Přečtěte si, jak pomocí ansible vytvořit frontu Azure Service Bus
keywords: ansible, azurové, devops, bash, playbook, service bus, fronta
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713233"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Kurz: Konfigurace front v Azure Service Bus pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření fronty
> * Vytvoření aplikace SAS
> * Načíst informace o oboru názvů
> * Načíst informace o frontě
> * Odvolat zásadu SAS fronty

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Vytvoření fronty služby Service Bus

Ukázkový kód playbooku vytvoří následující zdroje:
- Skupina prostředků Azure
- Obor názvů služby Service Bus v rámci skupiny prostředků
- Fronta služby Service Bus s oborem názvů

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Vytvoření zásad SAS

Sdílený [přístupový podpis (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) je mechanismus autorizace založený na deklaracích pomocí tokenů. 

Ukázkový kód playbooku vytvoří dvě zásady SAS pro frontu služby Service Bus s různými oprávněními.

Uložte následující ukázkový playbook jako `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
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

Před spuštěním playbooku se podívejte na následující poznámky:
- Hodnota `rights` představuje oprávnění, které má uživatel s frontou. Zadejte jednu z `manage` `listen`následujících hodnot: , , `send`, nebo `listen_send`.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Načíst informace o oboru názvů

Ukázkový kód playbooku se dotazuje na informace o oboru názvů.

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

Před spuštěním playbooku se podívejte na následující poznámky:
- Hodnota `show_sas_policies` označuje, zda se mají zobrazit zásady SAS pod zadaným oborem názvů. Ve výchozím nastavení `False` je hodnota, aby se zabránilo další zatížení sítě.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Načíst informace o frontě

Ukázkový kód playbooku dotazuje informace o frontě. 

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

Před spuštěním playbooku se podívejte na následující poznámky:
- Hodnota `show_sas_policies` označuje, zda se mají zobrazit zásady SAS pod zadanou frontou. Ve výchozím nastavení je `False` tato hodnota nastavena tak, aby se zabránilo další režii sítě.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odvolat zásadu SAS fronty

Ukázkový kód playbooku odstraní zásadu SAS fronty.

Uložte následující ukázkový playbook jako `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující `cleanup.yml`kód jako :

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Kurz: Konfigurace tématu v Azure Service Bus pomocí Ansible](ansible-service-bus-topic-configure.md)