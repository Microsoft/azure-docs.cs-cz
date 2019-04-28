---
title: Kurz – konfigurace směrovací tabulky Azure pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet, měnit a odstraňovat směrovací tabulky Azure pomocí Ansible
keywords: ansible, azure, devops, bash, playbooku, sítě, směrování, směrovací tabulky
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3d20a7bb98ba266850baa0512f5b767f8b649767
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764476"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Kurz: Konfigurovat směrovací tabulky Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure automaticky směruje provoz mezi Azure podsítěmi virtuálních sítí a místní sítí. Pokud potřebujete větší kontrolu nad směrování vaše prostředí, můžete vytvořit [směrovací tabulka](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Vytvoření směrovací tabulky vytvořit virtuální síť a podsíť přidružit podsíť zrušení spojení tabulku směrování z jedné podsítě vytvořit směrovací tabulku a odstranit směruje dotaz směrovací tabulku odstranit tabulku směrování

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Playbook kódu v této části se vytvoří směrovací tabulku. Informace o limitech směrovací tabulky najdete v tématu [omezení Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Uložte následující ukázkový playbook jako `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Playbook kódu v této části:

* Vytvoří virtuální síť
* Vytvoří podsíť ve virtuální síti
* Přidruží směrovací tabulky k podsíti

Směrovací tabulky nejsou přidružené k virtuálním sítím. Místo toho směrovacích tabulek jsou spojeny s podsítí virtuální sítě.

Virtuální sítě a směrovací tabulky musí existovat vedle sebe ve stejném umístění Azure a předplatné.

Podsítě a směrovací tabulky mají vztah jeden mnoho. Podsíť je definovat bez přidružené směrovací tabulky nebo jednu směrovací tabulku. Směrovací tabulky můžou být spojené s none, jeden nebo více podsítí. 

Provoz z podsítě se směruje na základě:

- trasy definované v rámci směrovací tabulky
- [Výchozí trasy](/azure/virtual-network/virtual-networks-udr-overview#default)
- šíření tras z místní sítě

Virtuální síť musí být připojen k bráně virtuální sítě Azure. Může jí být ExpressRoute nebo VPN při použití protokolu BGP s bránou sítě VPN.

Uložte následující ukázkový playbook jako `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušit přidružení směrovací tabulky z podsítě

Playbook kódu v této části dissociates směrovací tabulku z podsítě.

Při rušení přidružení směrovací tabulky z podsítě, nastavte `route_table` pro podsíť, kterou chcete `None`. 

Uložte následující ukázkový playbook jako `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Vytvoření trasy

Playbook kódu v této části trasy do směrovací tabulky. 

Uložte následující ukázkový playbook jako `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Před spuštěním playbooku, viz následující poznámky:

* `virtual_network_gateway` je definován jako `next_hop_type`. Další informace o tom, jak Azure vybere trasách najdete v tématu [Přehled směrování](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` je definován jako `10.1.0.0/16`. Předpona, která nelze duplikovány v rámci směrovací tabulky.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Odstranit trasu

Playbook kódu v této části odstraní trasy z tabulky trasy.

Uložte následující ukázkový playbook jako `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Získejte informace o tabulce směrování

Playbook kód v této části používá modul Ansible `azure_rm_routetable_facts` k načtení informací o tabulce směrování.

Uložte následující ukázkový playbook jako `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Odstranit tabulku směrování

Playbook kódu v této části směrovací tabulku.

Když se odstraní tabulku směrování, odstraní se také všechny jeho trasy.

Směrovací tabulku nelze odstranit, pokud se přidruží k podsíti. [Zrušit přidružení směrovací tabulky z podsítí](#dissociate-a-route-table-from-a-subnet) než se pokusíte odstranit tabulku směrování. 

Uložte následující ukázkový playbook jako `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)