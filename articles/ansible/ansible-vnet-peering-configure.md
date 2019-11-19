---
title: Kurz – konfigurace partnerského vztahu virtuálních sítí Azure pomocí Ansible
description: Naučte se používat Ansible k propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí.
keywords: Ansible, Azure, DevOps, bash, PlayBook, sítě, partnerský vztah
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155726"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Kurz: konfigurace partnerského vztahu virtuálních sítí Azure pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Partnerský vztah virtuálních sítí (VNET)](/azure/virtual-network/virtual-network-peering-overview) umožňuje bezproblémové připojení dvou virtuálních sítí Azure. Po navázání partnerského vztahu se tyto dvě virtuální sítě zobrazí jako jedna pro účely připojení. 

Provoz se směruje mezi virtuálními počítači ve stejné virtuální síti prostřednictvím privátních IP adres. Podobně provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován prostřednictvím páteřní infrastruktury Microsoftu. Výsledkem je, že virtuální počítače v různých virtuálních sítích můžou vzájemně komunikovat.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření dvou virtuálních sítí
> * Navázat partnerský vztah mezi dvěma virtuálními sítěmi
> * Odstranění partnerského vztahu mezi dvěma sítěmi

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Vytvoření dvou skupin prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vzorový kód PlayBook v této části se používá pro:

- Vytvoření dvou skupin prostředků 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Vytvoření první virtuální sítě

Vzorový kód PlayBook v této části se používá pro:

- Vytvoření virtuální sítě
- Vytvoření podsítě v rámci virtuální sítě

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Vytvoření druhé virtuální sítě

Vzorový kód PlayBook v této části se používá pro:

- Vytvoření virtuální sítě
- Vytvoření podsítě v rámci virtuální sítě

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Navázat partnerský vztah mezi dvěma virtuálními sítěmi

Vzorový kód PlayBook v této části se používá pro:

- Inicializace partnerského vztahu virtuálních sítí
- Partnerské dva dřív vytvořené virtuální sítě

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Odstranit partnerský vztah virtuální sítě

Vzorový kód PlayBook v této části se používá pro:

- Odstranit partnerský vztah mezi dvěma dříve vytvořenými virtuálními sítěmi

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Získat ukázkovou PlayBook

Existují dva způsoby, jak získat úplnou ukázkovou PlayBook:

- [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) a uložte ho do `vnet_peering.yml`.
- Vytvořte nový soubor s názvem `vnet_peering.yml` a zkopírujte do něj následující obsah:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Spuštění ukázkové PlayBook

Vzorový PlayBook kód v této části se používá k testování různých funkcí zobrazených v celém kurzu.

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- V části `vars` nahraďte zástupný text `{{ resource_group_name }}` názvem vaší skupiny prostředků.

Spusťte PlayBook pomocí příkazu Ansible-PlayBook:

```bash
ansible-playbook vnet_peering.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Vzorový kód PlayBook v této části se používá pro:

- Odstranit dvě skupiny prostředků vytvořené dříve

Uložte následující ukázkový playbook jako `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- Zástupný text `{{ resource_group_name-1 }}` nahraďte názvem první vytvořené skupiny prostředků.
- Zástupný text `{{ resource_group_name-2 }}` nahraďte názvem druhé skupiny prostředků vytvořenou.
- Všechny prostředky v rámci dvou zadaných skupin prostředků se odstraní.

Spusťte PlayBook pomocí příkazu Ansible-PlayBook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)