---
title: Kurz – konfigurace databází v Azure Database for MySQL pomocí Ansible
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat server Azure Database for MySQL.
keywords: ansible, azure, devops, bash, playbook, mysql, database
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f068b3022c94466a20b524240dc293392b1f42ff
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603127"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Kurz: Konfigurace databází v Azure Database for MySQL pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) je relační databázová služba založená na edici MySQL Community Edition. Azure Database for MySQL vám umožní spravovat databáze MySQL ve vašich webových aplikacích.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření serveru MySql
> * Vytvoření databáze MySql
> * Nakonfigurujte pravidlo brány firewall tak, aby se externí aplikace mohla připojit k vašemu serveru.
> * Připojení k serveru MySql z Azure Cloud shellu
> * Dotaz na dostupné servery MySQL
> * Vypíše všechny databáze na připojených serverech.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Vytvořit skupinu prostředků

Kód PlayBook v této části vytvoří skupinu prostředků Azure. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* Vytvoří se skupina prostředků s názvem `myResourceGroup`.
* Skupina prostředků se vytvoří v umístění `eastus`:

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Vytvoření serveru a databáze MySQL

Kód PlayBook v této části vytvoří server MySQL a instanci Azure Database for MySQL. Nový server MySQL je server pro základní účely s jednou vCore a má název `mysqlserveransible`. Instance databáze má název `mysqldbansible`.

Další informace o cenových úrovních najdete v tématu [Azure Database for MySQL cenové úrovně](/azure/mysql/concepts-pricing-tiers). 

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* V části `vars` musí být hodnota `mysqlserver_name` jedinečná.
* V části `vars` nahraďte `<server_admin_password>` heslem.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall

Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci připojit se k vašemu serveru prostřednictvím brány firewall služby Azure MySQL. Příklady externích aplikací jsou `mysql` nástroje příkazového řádku a MySQL Workbench.

Kód PlayBook v této části vytvoří pravidlo brány firewall s názvem `extenalaccess`, které umožňuje připojení z jakékoli externí IP adresy. 

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

Před spuštěním PlayBook se podívejte na následující poznámky:

* V části proměnných nahraďte `startIpAddress` a `endIpAddress`. Použijte rozsah IP adres, které odpovídají rozsahu, ze kterého se budete připojovat.
* Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.
* PlayBook používá modul `azure_rm_resource`, který umožňuje přímé použití REST API.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Připojení k serveru

V této části se pomocí služby Azure Cloud Shell připojíte k serveru, který jste vytvořili dříve.

1. Vyberte tlačítko **vyzkoušet** v následujícím kódu:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Na příkazovém řádku zadejte následující příkaz pro dotaz na stav serveru:

    ```sql
    mysql> status
    ```
    
    Pokud bude vše v dobrém, zobrazí se výstup podobný následujícímu výsledku:
    
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
    
## <a name="query-mysql-servers"></a>Dotazování serverů MySQL

Kód PlayBook v této části se dotazuje na servery MySQL v `myResourceGroup` a vypíše databáze na nalezených serverech.

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

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

Také se zobrazí následující výstup databáze MySQL:

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)