---
title: Kurz – konfigurace partnerského vztahu virtuální sítě Azure pomocí ansible
description: Přečtěte si, jak pomocí ansible připojit virtuální sítě s partnerským vztahem virtuální sítě.
keywords: ansible, azurové, devops, bash, playbook, networking, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155726"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Kurz: Konfigurace partnerského vztahu virtuální sítě Azure pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Partnerský vztah virtuální sítě (VNet)](/azure/virtual-network/virtual-network-peering-overview) umožňuje bezproblémové připojení dvou virtuálních sítí Azure. Po vzájemném vztahu se dvě virtuální sítě zobrazí jako jedna pro účely připojení. 

Provoz se směruje mezi virtuálními počítači ve stejné virtuální síti prostřednictvím privátních IP adres. Podobně provoz mezi virtuálními počítači v partnerské virtuální síti je směrován přes páteřní infrastrukturu Microsoftu. V důsledku toho virtuální počítače v různých virtuálních sítích můžete komunikovat mezi sebou.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření dvou virtuálních sítí
> * Peer dvě virtuální sítě
> * Odstranění partnerského vztahu mezi dvěma sítěmi

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Vytvoření dvou skupin prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Ukázkový kód playbooku v této části slouží k:

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

Ukázkový kód playbooku v této části slouží k:

- Vytvoření virtuální sítě
- Vytvoření podsítě ve virtuální síti

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

Ukázkový kód playbooku v této části slouží k:

- Vytvoření virtuální sítě
- Vytvoření podsítě ve virtuální síti

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

## <a name="peer-the-two-virtual-networks"></a>Peer dvě virtuální sítě

Ukázkový kód playbooku v této části slouží k:

- Inicializace partnerského vztahu virtuální sítě
- Peer dva dříve vytvořené virtuální sítě

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

## <a name="delete-the-virtual-network-peering"></a>Odstranění partnerského vztahu virtuální sítě

Ukázkový kód playbooku v této části slouží k:

- Odstranění partnerského vztahu mezi dvěma dříve vytvořenými virtuálními sítěmi

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Získejte ukázkový playbook

Existují dva způsoby, jak získat kompletní ukázkový playbook:

- [Stáhněte si playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) `vnet_peering.yml`a uložte jej do .
- Vytvořte nový `vnet_peering.yml` soubor s názvem a zkopírujte do něj následující obsah:

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

## <a name="run-the-sample-playbook"></a>Spuštění ukázkového playbooku

Ukázkový kód playbooku v této části se používá k testování různých funkcí zobrazených v tomto kurzu.

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- V `vars` části nahraďte `{{ resource_group_name }}` zástupný symbol názvem skupiny prostředků.

Spusťte playbook pomocí příkazu ansible-playbook:

```bash
ansible-playbook vnet_peering.yml
```

Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Ukázkový kód playbooku v této části slouží k:

- Odstranění dvou skupin prostředků vytvořených dříve

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

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- Nahraďte `{{ resource_group_name-1 }}` zástupný symbol názvem první vytvořené skupiny prostředků.
- Nahraďte `{{ resource_group_name-2 }}` zástupný symbol názvem druhé vytvořené skupiny prostředků.
- Všechny prostředky v rámci dvou určených skupin prostředků budou odstraněny.

Spusťte playbook pomocí příkazu ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)