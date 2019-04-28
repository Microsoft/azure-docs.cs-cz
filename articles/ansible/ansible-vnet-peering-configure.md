---
title: Kurz – konfigurace partnerského vztahu virtuální sítě Azure, pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k propojení virtuálních sítí s využitím partnerského vztahu virtuální sítě.
keywords: ansible, azure, devops, bash, playbooku, sítě, partnerský vztah
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: d3d1aab17a94e928d113246f280c3391cae85655
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767086"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Kurz: Nakonfigurujte partnerský vztah virtuální sítě Azure, pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Partnerské vztahy virtuálních sítí (VNet)](/azure/virtual-network/virtual-network-peering-overview) umožňuje bezproblémové propojení dvou virtuálních sítí Azure. Po navázání partnerského vztahu se dvěma virtuálními sítěmi se jeví jako jedna pro účely připojení. 

Provoz se směruje mezi virtuálními počítači ve stejné virtuální síti pomocí soukromých IP adres. Podobně provoz mezi virtuálními počítači v partnerské virtuální síti je směrován přes páteřní infrastrukturu Microsoftu. V důsledku toho virtuálních počítačů v různých virtuálních sítích komunikovat mezi sebou.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření dvou virtuálních sítí
> * Navázání partnerského vztahu mezi dvěma virtuálními sítěmi
> * Odstranit partnerský vztah mezi těmito dvěma sítěmi

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Vytvořit dvě skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vzorový kód playbook v této části se používá pro:

- Vytvořit dvě skupiny prostředků 

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

Vzorový kód playbook v této části se používá pro:

- Vytvoření virtuální sítě
- Vytvořte podsíť ve virtuální síti

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

Vzorový kód playbook v této části se používá pro:

- Vytvoření virtuální sítě
- Vytvořte podsíť ve virtuální síti

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

## <a name="peer-the-two-virtual-networks"></a>Navázání partnerského vztahu mezi dvěma virtuálními sítěmi

Vzorový kód playbook v této části se používá pro:

- Inicializovat partnerský vztah virtuální sítě
- Navázání partnerského vztahu mezi dvěma dříve vytvořili virtuální sítě

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

Vzorový kód playbook v této části se používá pro:

- Odstranit partnerský vztah mezi těmito dvěma dříve vytvořili virtuální sítě

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Získat ukázky playbook

Existují dva způsoby, jak získat playbook úplnou ukázku:

- [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) a uložit ho. tím `vnet_peering.yml`.
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

## <a name="run-the-sample-playbook"></a>Spustit playbook vzorku

Vzorový kód playbook v této části se používá k testování různých funkcí zobrazených v celém tomto kurzu.

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.

Spusťte playbook ansible playbook příkazem:

```bash
ansible-playbook vnet_peering.yml
```

Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

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

Vzorový kód playbook v této části se používá pro:

- Odstranění skupiny dva prostředky vytvořili dříve

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

Tady jsou některé důležité poznámky vzít v úvahu při práci s playbook ukázka:

- Nahradit `{{ resource_group_name-1 }}` vytvořili zástupný symbol s názvem první skupinu prostředků.
- Nahradit `{{ resource_group_name-2 }}` zástupný symbol s názvem skupiny prostředků, druhém vytvořili.
- Odstraní všechny prostředky v rámci dvou skupin zadaný prostředek.

Spusťte playbook ansible playbook příkazem:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)