---
title: Kurz – konfigurace témat v Azure Service Bus pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytvoření tématu Azure Service Bus
keywords: ansible, azure, devops, bash, playbook, služby Service bus, témat a předplatných
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: f7c6df05c19683fe739520f622e6bcab8478cbd9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767135"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Kurz: Konfigurace témat v Azure Service Bus pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření tématu
> * Vytvoření odběru
> * Vytvořit zásady SAS
> * Načíst informace o oboru názvů
> * Načtení informací o téma a odběr
> * Odvolat zásady SAS

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Vytvoření tématu služby Service Bus

Playbook vzorový kód vytvoří následující prostředky:
- Skupina prostředků Azure
- Obor názvů služby Service Bus v rámci skupiny prostředků
- Téma služby Service Bus s oborem názvů

Uložte následující ukázkový playbook jako `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Vytvoření odběru

Playbook vzorový kód vytvoří odběr v rámci tématu služby Service Bus. Témata služby Azure Service Bus může obsahovat víc předplatných. Odběratel do tématu může obdrží kopii každá zpráva odeslaná do tématu. Předplatná jsou pojmenované entity, které jsou trvale vytvořil, ale můžete volitelně vyprší.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Uložte následující ukázkový playbook jako `servicebus_subscription.yml`:

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Vytvořit zásady SAS.

A [sdíleného přístupového podpisu (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) virtuálních sítí je mechanismus ověřování na základě deklarací identity pomocí tokenů. 

Playbook vzorový kód vytvoří dvě zásady SAS pro frontu služby Service Bus pomocí jiné oprávnění.

Uložte následující ukázkový playbook jako `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Načtení informací o téma a odběr

Ukázkové dotazy kód playbook pro následující informace:
- Informace o tématu služby Service Bus
- Seznam podrobností o předplatné tématu
 
Uložte následující ukázkový playbook jako `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Před spuštěním playbooku, viz následující poznámky:
- `show_sas_policies` Hodnota označuje, jestli se má zobrazit zásady SAS pod zadanou frontu. Ve výchozím nastavení, tato hodnota nastavená na `False` aby se zabránilo další síťové zatížení.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible v Azure](/azure/ansible/)