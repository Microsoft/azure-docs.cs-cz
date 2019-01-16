---
title: Vytvoření, změna nebo odstranění Azure směrovací tabulky pomocí Ansible
description: Zjistěte, jak použít Ansible k vytvoření, změna nebo odstranění směrovací tabulky pomocí Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, sítě, směrování, směrovací tabulky
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 29672a75408e42fb9239e5d826784b46e7280805
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332264"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Vytvoření, změna nebo odstranění Azure směrovací tabulky pomocí Ansible
Azure automaticky směruje provoz mezi Azure podsítěmi virtuálních sítí a místní sítí. Pokud chcete změnit výchozí směrování v Azure, můžete udělat tak, že vytvoříte [směrovací tabulka](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. Tento článek ukazuje, jak vytvořit, změnit nebo odstranit Azure směrovací tabulku a připojte směrovací tabulky k podsíti také. 

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 je potřeba spustit playbooky následující ukázka v tomto kurzu.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky
Tato část nabízí ukázkové playbook Ansible, který vytvoří směrovací tabulku. Platí omezení na počet směrovacích tabulek můžete vytvořit na umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

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

Uložit tento playbook jako `route_table_create.yml`. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti
Podsíť může mít žádnou nebo jednu směrovací tabulky přidružené k němu. Směrovací tabulky můžou být přidružené k nula nebo více podsítí. Protože směrovací tabulky nejsou přidružené k virtuálním sítím, třeba přidružení směrovací tabulky do každé podsítě má směrovací tabulky přidružené k. Všechny přenosů z podsítě se směruje podle trasy, které jste vytvořili v rámci směrovacích tabulek [výchozí trasy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default), a šíření tras z místní sítě, pokud virtuální síť připojená k (bránu virtuální sítě Azure ExpressRoute nebo VPN, pokud pomocí protokolu BGP s bránou VPN). Můžete přidružit pouze směrovací tabulku do podsítí ve virtuálních sítích, které existují ve stejném umístění Azure a předplatné jako směrovací tabulky.

Tato část představuje playbook Ansible ukázka, která vytvoří virtuální síť a podsíť, a pak přidruží směrovací tabulky k podsíti.

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

Uložit tento playbook jako `route_table_associate.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušit přidružení směrovací tabulky z podsítě
Pokud zrušíte přidružení směrovací tabulky z podsítě, stačí nastavit `route_table` v jedné podsítě do `None`. Níže je playbook ansible vzorku. 

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

Uložit tento playbook jako `route_table_dissociate.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Vytvoření trasy
Tato část nabízí ukázkové playbook Ansible, který vytváří trasu pod směrovací tabulky. Definuje `virtual_network_gateway` jako `next_hop_type` a `10.1.0.0/16` jako `address_prefix`. Předpona, která nelze zkopírovat do víc tras ve směrovací tabulce, i když předpona, která může být v rámci jinou předponu. Další informace o tom, jak Azure vybírá trasy a podrobný popis všech typů dalších segmentů směrování, najdete v článku [Přehled směrování](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

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
Uložit tento playbook jako `route_create.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Odstranit trasu
Tato část nabízí ukázkové playbook Ansible, který odstraní trasy z tabulky trasy.

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

Uložit tento playbook jako `route_delete.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Získejte informace ze směrovací tabulky
Můžete zobrazit podrobnosti o route_table Ansible modul s názvem `azure_rm_routetable_facts`. Modul skutečnosti vrátí informace směrovací tabulky všechny trasy k němu připojená.
Níže je playbook ansible vzorku. 

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

Uložit tento playbook jako `route_table_facts.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Odstranit tabulku směrování
Směrovací tabulka je přidružen k žádné podsítě, nelze odstranit. [Zrušit přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulku ze všech podsítí, než se pokusíte odstranit.

Můžete odstranit tabulku směrování spolu s všechny trasy. Níže je playbook ansible vzorku. 

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

Uložit tento playbook jako `route_table_delete.yml`. Playbook Ansible spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)
