---
title: Správa serveru – Azure CLI-Azure Database for PostgreSQL-flexibilní Server
description: Naučte se spravovat Azure Database for PostgreSQL flexibilní Server z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8e24dd6cb8a1fa90f1a6caf9117ab3c344c00b12
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913870"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Správa Azure Database for PostgreSQL flexibilního serveru pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto článku se dozvíte, jak spravovat flexibilní Server nasazený v Azure. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="prerequisites"></a>Předpoklady
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Pokud jste ještě nevytvořili flexibilní Server, vytvořte si ho prosím, abyste mohli začít s tímto průvodcem.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Pomocí následujícího příkazu můžete snadno škálovat výpočetní vrstvu, virtuální jádra a úložiště. Můžete zobrazit všechny operace serveru, které můžete spustit [AZ Postgres Flexible-Server Server Overview](https://docs.microsoft.com/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Zde jsou uvedeny podrobnosti o argumentech výše:

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zadejte jedinečný název serveru. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name|Standard_D4ds_v3|Zadejte název výpočetní úrovně a velikosti. Postupuje podle konvence Standard_ {VM Size} ve zkráceném formátu. Další informace najdete v [cenové úrovni](../concepts-pricing-tiers.md) .
velikost úložiště | 6144 | Kapacita úložiště serveru (jednotkou jsou megabajty). Minimální 5120 a zvyšuje se v 1024 přírůstcích.

> [!IMPORTANT]
> Velikost úložiště nejde škálovat. 

## <a name="manage-postgresql-databases-on-a-server"></a>Správa databází PostgreSQL na serveru

Existuje řada aplikací, které můžete použít pro připojení k vašemu serveru Azure Database for PostgreSQL. Pokud má klientský počítač nainstalovaný systém PostgreSQL, můžete se připojit k serveru Azure PostgreSQL pomocí místní instance nástroje [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Teď se pomocí nástroje příkazového řádku psql připojíme k serveru Azure PostgreSQL.

1. Spusťte následující příkaz psql pro připojení k serveru Azure Database for PostgreSQL:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Třeba tento příkaz provádí pomocí přihlašovacích údajů pro přístup připojení k výchozí databázi s názvem **postgres** na serveru PostgreSQL **mydemoserver.postgres.database.azure.com** . Zadejte heslo `<server_admin_password>`, které jste zvolili při zobrazení výzvy k zadání hesla.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Jakmile budete připojeni, nástroj psql zobrazí příkazový řádek postgres, kde můžete zadávat příkazy jazyka SQL. Ve výstupu počátečního připojení se může zobrazit upozornění, protože nástroj psql, který používáte, může mít jinou verzi než na serveru Azure Database for PostgreSQL.

   Příklad výstupu nástroje psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Pokud brána firewall není nakonfigurovaná k povolení IP adresy vašeho klienta, dojde k následující chybě:
   >
   > "psql: závažná chyba: není k dispozici žádná pg_hba. conf pro hostitele `<IP address>` , uživatele" myadmin ", databázi" Postgres ", protokol SSL je povinný: připojení SSL je povinné. Zadejte možnosti SSL a zkuste to znovu.
   >
   > Ověřte, že je v kroku pravidla brány firewall povolená IP adresa klienta.

2. Na příkazovém řádku vytvořte prázdnou databázi s názvem "postgresdb" zadáním následujícího příkazu:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Na příkazovém řádku spusťte následující příkaz, který přepne připojení na nově vytvořenou databázi **postgresdb** :

    ```bash
    \c postgresdb
    ```

4. Zadejte `\q` a pak stisknutím klávesy Enter ukončete nástroj psql.

Připojili jste se k serveru Azure Database for PostgreSQL pomocí nástroje psql a vytvořili jste prázdnou uživatelskou databázi.

## <a name="reset-admin-password"></a>Resetovat heslo správce
Pomocí tohoto příkazu můžete změnit heslo role správce.
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Ujistěte se, že heslo má minimálně 8 znaků a maximálně 128 znaků.
> Heslo musí obsahovat znaky ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.

## <a name="delete-a-server"></a>Odstranění serveru

Pokud byste chtěli jenom odstranit PostgreSQL flexibilní Server, můžete spustit příkaz [AZ Postgres Flexible-Server Delete](https://docs.microsoft.com/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

- [Vysvětlení konceptů zálohování a obnovení](concepts-backup-restore.md)
- [Vyladění a sledování serveru](concepts-monitoring.md)