---
title: Kurz – konfigurace databáze ve službě Azure Database for MySQL pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat server Azure Database for MySQL.
keywords: ansible, azure, devops, bash, playbook, mysql, database
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 7238e993ebd812734b3b08f57b7a4c2f080a7384
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764070"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Kurz: Konfigurovat databáze ve službě Azure Database for MySQL pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) je služba relační databáze založené na MySQL Community Edition. Azure Database for MySQL umožňuje správu databází MySQL ve službě web apps.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření serveru MySql
> * Vytvoření databáze MySql
> * Konfigurace pravidla filewall tak, aby externí aplikace se může připojit k serveru
> * Připojení k MySql serveru z Azure cloud shell.
> * Dotazy k dispozici servery MySQL
> * Vypsat všechny databáze v připojených serverech

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Playbook kód v této části se vytvoří skupina prostředků Azure. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Uložte následující ukázkový playbook jako `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Před spuštěním playbooku, viz následující poznámky:

* Skupina prostředků s názvem `myResourceGroup` se vytvoří.
* Skupina prostředků se vytvořila v `eastus` umístění:

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Vytvoření serveru a databáze MySQL

Playbook kódu v této části se vytvoří MySQL server a Azure Database for MySQL – instance. Nový server MySQL je generace 5 základní účel serveru s jedno virtuální jádro a je pojmenován `mysqlserveransible`. Pojmenované instance databáze `mysqldbansible`.

Další informace o cenových úrovních najdete v tématu [– Azure Database for MySQL cenové úrovně](/azure/mysql/concepts-pricing-tiers). 

Uložte následující ukázkový playbook jako `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Před spuštěním playbooku, viz následující poznámky:

* V `vars` části hodnotu `mysqlserver_name` musí být jedinečný.
* V `vars` části, nahraďte `<server_admin_password>` s heslem.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall

Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci pro připojení k serveru prostřednictvím brány firewall služby Azure MySQL. Příkladem externí aplikace jsou `mysql` nástroj příkazového řádku a aplikace MySQL Workbench.

Playbook kód v této části se vytvoří pravidlo brány firewall s názvem `extenalaccess` , který umožňuje připojení z libovolné externí IP adresy. 

Uložte následující ukázkový playbook jako `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Před spuštěním playbooku, viz následující poznámky:

* V sekci proměnných nahradit `startIpAddress` a `endIpAddress`. Použijte rozsah IP adres, které odpovídají rozsahu, ze kterého budete připojení.
* Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.
* Playbook používá `azure_rm_resource` modul, který umožňuje přímé použití rozhraní REST API.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Připojení k serveru

V této části použít Azure cloud shell se připojit k serveru, který jste vytvořili dříve.

1. Vyberte **vyzkoušet** tlačítko v následujícím kódu:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Do příkazového řádku zadejte následující příkaz, který dotaz na stav serveru:

    ```sql
    mysql> status
    ```
    
    Pokud všechno proběhne správně, zobrazí se výstup podobný následující výsledky:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Servery MySQL dotazu

Playbook kódu v této části se dotazuje MySQL serverů v `myResourceGroup` a seznam databází na serveru nalezen.

Uložte následující ukázkový playbook jako `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook mysql_query.yml
```

Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Můžete také zobrazit následující výstup pro databázi MySQL:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující ukázkový playbook jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)