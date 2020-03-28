---
title: Kurz – konfigurace směrovacích tabulek Azure pomocí ansible
description: Naučte se vytvářet, spravovat a odstraňovat tabulky směrování Azure pomocí Ansible. Také se dozvíte, jak vytvářet a odstraňovat trasy.
keywords: ansible, azurové, devops, bash, playbook, networking, trasa, směrovací stůl
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659793"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Kurz: Konfigurace směrovacích tabulek Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud potřebujete větší kontrolu nad směrováním prostředí, můžete vytvořit [směrovací tabulku](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Vytvoření tabulky postupu Vytvoření virtuální sítě a podsítě Přidružení směrovací tabulky k podsíti Zrušení postupu z podsítě Vytvořit a odstranit trasy Dotaz na tabulku postupu Odstranění směrovací tabulky

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Kód playbooku v této části vytvoří směrovací tabulku. Informace o omezeních tabulky tras najdete v tématu [Limity Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Playbook kód v této části:

* Vytvoří virtuální síť
* Vytvoří podsíť ve virtuální síti.
* Přidružení tabulky postupu k podsíti

Tabulky směrování nejsou přidruženy k virtuálním sítím. Spíše jsou tabulky tras přidruženy k podsíti virtuální sítě.

Virtuální síť a směrovací tabulka musí existovat vedle sebe ve stejném umístění Azure a předplatném.

Podsítě a směrovací tabulky mají relaci 1:N. Podsíť lze definovat bez přidružené tabulky tras nebo s jednou směrovací tabulkou. Tabulky tras lze přidružit k žádné, jedné nebo mnoha podsítím. 

Provoz z podsítě je směrován na základě:

- trasy definované v tabulkách tras
- [výchozí trasy](/azure/virtual-network/virtual-networks-udr-overview#default)
- trasy šířené z místní sítě

Virtuální síť musí být připojena k bráně virtuální sítě Azure. Brána může být ExpressRoute, nebo VPN, pokud používáte Protokol BGP s bránou VPN.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Odložky směrovací tabulky od podsítě

Kód playbooku v této části odpojí směrovací tabulku od podsítě.

Při disociaci tabulky tras od podsítě `route_table` nastavte podsíť `None`na . 

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Vytvoření trasy

Kód playbooku v této části je postup v rámci směrovací tabulky. 

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

Před spuštěním playbooku se podívejte na následující poznámky:

* `virtual_network_gateway`je definována jako `next_hop_type`. Další informace o tom, jak Azure vybírá trasy, najdete v [tématu Přehled směrování](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix`je definována jako `10.1.0.0/16`. Předponu nelze v tabulce postupu duplikovat.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Odstranění trasy

Kód playbooku v této části odstraní trasu ze směrovací tabulky.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Získání informací o tabulce tras

Kód playbooku v této části používá `azure_rm_routetable_facts` modul Ansible k načtení informací o směrovací tabulce.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Odstranění směrovací tabulky

Kód playbooku v této části směrovací tabulka.

Při odstranění směrovací tabulky jsou odstraněny také všechny její trasy.

Směrovací tabulku nelze odstranit, pokud je přidružena k podsíti. [Před pokusem o odstranění směrovací tabulky odkreslujte směrovací tabulku od všech podsítí.](#dissociate-a-route-table-from-a-subnet) 

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)