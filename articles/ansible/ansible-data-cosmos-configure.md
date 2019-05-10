---
title: Kurz – konfigurace účtů služby Azure Cosmos DB pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k vytvoření a konfigurace služby Azure Cosmos DB
keywords: ansible v azure, devops, bash, playbooku, cosmo db, databáze
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 06d416358c1886f09b0b2336cc1ea53ce89947ae
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230812"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>Kurz: Konfigurace účtů služby Azure Cosmos DB pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/) je databázová služba, která podporuje několik typů databáze. Tyto typy databází zahrnují dokument, klíč hodnota, širokých sloupců a grafů. Pomocí Ansible, můžete automatizovat nasazení a konfigurace prostředků ve vašem prostředí.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvořit účet
> * Načtení klíčů účtu
> * Odstranit účet

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Vytvořit náhodný přípony

Playbook ukázka vytvoří náhodný přípony. Příponové se používá jako součást názvu účtu Azure Cosmos DB.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků 

Playbook ukázka vytvoří skupinu prostředků Azure. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Následující kód vytvoří virtuální síť a podsíť pro účet služby Azure Cosmos DB:

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Vytvořit účet Azure Cosmos DB

Následující kód vytvoří účet služby Cosmos DB:

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

Vytvoření účtu trvá několik minut.

## <a name="retrieve-the-keys"></a>Načtení klíčů

Následující kód načte klíče k použití ve vaší aplikaci.

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Odstranění účtu služby Azure Cosmos DB

A konečně poslední fragment kódu ukazuje, jak odstranit účet služby Azure Cosmos DB.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Získat ukázky playbook

Existují dva způsoby, jak získat playbook úplnou ukázku:
- [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml) a uložit ho. tím `cosmosdb.yml`.
- Vytvořte nový soubor s názvem `cosmosdb.yml` a zkopírujte do něj následující obsah:

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

V této části se spusťte playbook pro testování různých funkcí uvedených v tomto článku.

Před spuštěním playbooku, proveďte následující změny:
- V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.
- Ujistěte se, "cosmosdbaccount_name obsahuje jenom malá písmena a je globálně jedinečný.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)