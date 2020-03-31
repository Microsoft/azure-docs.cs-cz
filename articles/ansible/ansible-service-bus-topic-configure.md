---
title: Kurz – konfigurace témat v Azure Service Bus pomocí Ansible
description: Přečtěte si, jak pomocí ansible vytvořit téma Azure Service Bus
keywords: ansible, azure, devops, bash, playbook, service bus, témata, předplatná
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155790"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Kurz: Konfigurace témat v Azure Service Bus pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření tématu
> * Vytvoření odběru
> * Vytvoření zásadY SAS
> * Načíst informace o oboru názvů
> * Načtení tématu a informací o předplatném
> * Odvolat zásadu SAS

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Vytvoření tématu Sběrnice

Ukázkový kód playbooku vytvoří následující zdroje:
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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Vytvoření předplatného

Ukázkový kód playbooku vytvoří předplatné v rámci tématu Service Bus. Témata Azure Service Bus můžou mít víc předplatných. Odběratel tématu může obdržet kopii každé zprávy odeslané do tématu. Odběry jsou pojmenované entity, které jsou trvale vytvořeny, ale může volitelně vyprší.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Vytvoření zásad SAS

Sdílený [přístupový podpis (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) je mechanismus autorizace založený na deklaracích pomocí tokenů. 

Ukázkový kód playbooku vytvoří dvě zásady SAS pro frontu služby Service Bus s různými oprávněními.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Načtení tématu a informací o předplatném

Ukázkový kód playbooku se dotazuje na následující informace:
- Informace o tématu služby Service Bus
- Seznam podrobností o předplatném tématu
 
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

Před spuštěním playbooku se podívejte na následující poznámky:
- Hodnota `show_sas_policies` označuje, zda se mají zobrazit zásady SAS pod zadanou frontou. Ve výchozím nastavení je `False` tato hodnota nastavena tak, aby se zabránilo další režii sítě.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_list.yml
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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)