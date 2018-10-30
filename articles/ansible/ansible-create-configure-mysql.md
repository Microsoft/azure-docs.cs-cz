---
title: Vytvoření a konfigurace serveru Azure Database for MySQL pomocí Ansible (Preview)
description: Zjistěte, jak pomocí Ansible vytvořit a nakonfigurovat server Azure Database for MySQL.
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, database
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: b549aeaf24bd774245ee1f2ff6924ac1f6dbeee3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427892"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible-preview"></a>Vytvoření a konfigurace serveru Azure Database for MySQL pomocí Ansible (Preview)
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) je spravovaná služba, která slouží k provozu, správě a škálování vysoce dostupných databází MySQL v cloudu. Ansible umožňuje automatizovat nasazování a konfiguraci prostředků ve vašem prostředí. 

V tomto rychlém startu se dozvíte, jak pomocí Ansible vytvořit server Azure Database for MySQL a nakonfigurovat jeho pravidlo brány firewall. Tyto úlohy můžete dokončit přibližně během pěti minut pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ke spuštění následujících ukázkových playbooků v tomto kurzu se vyžaduje Ansible 2.7. Spuštěním příkazu `sudo pip install ansible[azure]==2.7.0rc2` můžete nainstalovat verzi Ansible 2.7 RC. Po vydání Ansible 2.7 už tady nebudete muset zadávat verzi, protože výchozí verze bude 2.7.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.  

Následující příklad vytvoří skupinu prostředků **myResourceGroup** v umístění **eastus**:

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

Uložte předchozí playbook jako **rg.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Vytvoření serveru a databáze MySQL
Následující příklad vytvoří server MySQL **mysqlserveransible** a instanci Azure Database for MySQL **mysqldbansible**. Jedná se o základní server pro obecné účely 5. generace s jedním virtuálním jádrem. 

Hodnota **mysqlserver_name** musí být jedinečná. Vysvětlení platných hodnot pro jednotlivé oblasti a úrovně najdete v [dokumentaci k cenovým úrovním](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Nahraďte `<server_admin_password>` heslem.

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

Uložte předchozí playbook jako **mysql_create.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall
Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure MySQL. Příkladem externí aplikace je nástroj pro příkazový řádek **mysql** nebo MySQL Workbench.
Následující příklad vytvoří pravidlo brány firewall **externalaccess**, které povolí připojení z jakékoli externí IP adresy. 

Místo **startIpAddress** a **endIpAddress** zadejte vlastní hodnoty. Použijte rozsah IP adres, který odpovídá umístění, ze kterého se budete připojovat. 

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

> [!NOTE]
> Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.
> 

Tady se k provedení této úlohy používá modul **azure_rm_resource**. Ten povolí přímé používání rozhraní REST API.

Uložte předchozí playbook jako **mysql_firewall.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Připojení k serveru pomocí nástroje pro příkazový řádek
[MySQL si můžete stáhnout](https://dev.mysql.com/downloads/) a nainstalovat do svého počítače. Místo toho můžete vybrat tlačítko **Vyzkoušet** ve vzorových kódech nebo tlačítko **>_** v pravém horním panelu nástrojů na webu Azure Portal a otevřít **Azure Cloud Shell**.

Zadejte další příkazy: 

1. Připojení k serveru pomocí nástroje pro příkazový řádek **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Zobrazení stavu serveru:
```sql
 mysql> status
```

Pokud vše půjde dobře, měl by výstupem nástroje příkazového řádku být následující text:

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

## <a name="using-facts-to-query-mysql-servers"></a>Dotazování serverů MySQL s využitím faktů
Následující příklad dotazuje servery MySQL ve skupině prostředků **myResourceGroup** a následně všechny databáze na příslušných serverech:

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

Uložte předchozí playbook jako **mysql_query.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:

```bash
ansible-playbook mysql_query.yml
```

Pak se pro server MySQL zobrazí následující výstup: 
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

Zobrazí se také následující výstup pro databázi MySQL:
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

Pokud tyto prostředky nepotřebujete, můžete je odstranit spuštěním následujícího příkladu. Ten odstraní skupinu prostředků **myResourceGroup**. 

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

Uložte předchozí playbook jako **rg_delete.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook rg_delete.yml
```

Pokud chcete odstranit pouze nově vytvořený server MySQL, spusťte následující příklad:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Uložte předchozí playbook jako **mysql_delete.yml**. Playbook spustíte pomocí příkazu **ansible-playbook** následujícím způsobem:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Ansible v Azure](https://docs.microsoft.com/azure/ansible/)
