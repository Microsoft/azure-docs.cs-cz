---
title: Kurz – konfigurace směrovacích tabulek Azure pomocí Ansible
description: Naučte se vytvářet, spravovat a odstraňovat směrovací tabulky Azure pomocí Ansible. Také se dozvíte, jak vytvořit a odstranit trasy.
keywords: Ansible, Azure, DevOps, bash, PlayBook, sítě, směrování, směrovací tabulka
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659793"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Kurz: Konfigurace směrovacích tabulek Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud potřebujete větší kontrolu nad směrováním vašeho prostředí, můžete vytvořit [směrovací tabulku](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Vytvoření směrovací tabulky vytvoření virtuální sítě a podsítě přidružení směrovací tabulky k podsíti zrušení přidružení směrovací tabulky z podsítě vytvoření a odstranění tras dotazování směrovací tabulky odstranění směrovací tabulky

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Kód PlayBook v této části vytvoří směrovací tabulku. Informace o omezeních tabulky trasy najdete v tématu [omezení Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

PlayBook kód v této části:

* Vytvoří virtuální síť.
* Vytvoří podsíť v rámci virtuální sítě.
* Přidruží směrovací tabulku k podsíti.

Směrovací tabulky nejsou přidružené k virtuálním sítím. Místo toho jsou směrovací tabulky přidruženy k podsíti virtuální sítě.

Tato virtuální síť a tabulka směrování musí existovat ve stejném umístění a předplatném Azure.

Podsítě a směrovací tabulky mají relaci 1: n. Podsíť může být definovaná bez přidružené směrovací tabulky ani jedné směrovací tabulky. Směrovací tabulky mohou být přidruženy k žádné, jedné nebo mnoha podsítím. 

Provoz z podsítě se směruje na základě těchto požadavků:

- trasy definované v rámci směrovacích tabulek
- [výchozí trasy](/azure/virtual-network/virtual-networks-udr-overview#default)
- trasy šířené z místní sítě

Virtuální síť musí být připojená k bráně virtuální sítě Azure. Brána může být ExpressRoute nebo VPN, pokud používáte protokol BGP s bránou VPN.

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušení přidružení směrovací tabulky z podsítě

PlayBook kód v této části dissociates směrovací tabulku z podsítě.

Při ruší směrovací tabulky z podsítě nastavte `route_table` pro `None`podsítě. 

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Vytvoření trasy

PlayBook kód v této části trasy v směrovací tabulce. 

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* `virtual_network_gateway` je definován jako `next_hop_type`. Další informace o tom, jak Azure vybírá trasy, najdete v tématu [Přehled směrování](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` je definován jako `10.1.0.0/16`. Předpona nemůže být duplikována v tabulce směrování.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Odstranění trasy

Kód PlayBook v tomto oddílu Odstraní trasu z směrovací tabulky.

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Získat informace o směrovací tabulce

Kód PlayBook v této části používá modul Ansible `azure_rm_routetable_facts` k načtení informací o tabulce směrování.

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Odstranění směrovací tabulky

PlayBook kód v této části a směrovací tabulku.

Při odstranění směrovací tabulky se odstraní také všechny její trasy.

Směrovací tabulku nelze odstranit, pokud je přidružena k podsíti. Před pokusem o odstranění směrovací tabulky zrušte [přidružení směrovací tabulky ze všech podsítí](#dissociate-a-route-table-from-a-subnet) . 

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)