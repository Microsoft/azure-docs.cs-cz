---
title: Správa serveru – Azure CLI-Azure Database for PostgreSQL-flexibilní Server
description: Naučte se spravovat Azure Database for PostgreSQL flexibilní Server z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493674"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Správa Azure Database for PostgreSQL flexibilního serveru pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto článku se dozvíte, jak spravovat flexibilní Server nasazený v Azure. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

V místním prostředí budete muset spustit Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Přihlaste se ke svému účtu pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Vyberte své předplatné pomocí příkazu [AZ Account set](/cli/azure/account) . Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu pro **odběr** v následujícím příkazu. Pokud máte více předplatných, vyberte předplatné, ke kterému se má prostředek fakturovat. Pokud chcete identifikovat všechna Vaše předplatná, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list) .

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Pokud jste ještě nevytvořili flexibilní Server, budete ho muset udělat, abyste mohli postupovat podle tohoto návodu.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Pomocí následujícího příkazu můžete snadno škálovat výpočetní vrstvu, virtuální jádra a úložiště. Seznam všech operací serveru, které můžete spustit, najdete v tématu [AZ Postgres Flexible-Server](/cli/azure/postgres/flexible-server) Overview.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Níže jsou uvedeny podrobnosti o argumentech v předchozím kódu:

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zadejte jedinečný název serveru. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí obsahovat 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name|Standard_D4ds_v3|Zadejte název výpočetní úrovně a velikosti. Hodnota se řídí konvencí *Standard_ {VM Size}* v zkrácených hodnotách. Další informace najdete v [cenové úrovni](../concepts-pricing-tiers.md) .
velikost úložiště | 6144 | Zadejte kapacitu úložiště serveru v megabajtech. Minimum je 5120, což roste v přírůstcích po 1024.

> [!IMPORTANT]
> Úložiště nemůžete škálovat. 

## <a name="manage-postgresql-databases-on-a-server"></a>Správa databází PostgreSQL na serveru

Existuje řada aplikací, které můžete použít pro připojení k vašemu serveru Azure Database for PostgreSQL. Pokud je v klientském počítači nainstalovaný PostgreSQL, můžete použít místní instanci [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Pojďme teď použít nástroj příkazového řádku psql pro připojení k serveru Azure Database for PostgreSQL.

1. Spusťte následující příkaz **psql** :

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Například následující příkaz se připojí k výchozí databázi s názvem **Postgres** na serveru PostgreSQL **mydemoserver.Postgres.Database.Azure.com** prostřednictvím přihlašovacích údajů pro přístup. Po zobrazení výzvy zadejte `<server_admin_password>` , kterou jste zvolili.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Po připojení se nástroj psql zobrazí na příkazovém řádku **Postgres** , kde můžete zadat příkazy SQL. Pokud je verze psql, která se používá, odlišná od verze na serveru Azure Database for PostgreSQL, zobrazí se upozornění ve výstupu počátečního připojení.

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
   > "psql: závažná chyba: není k dispozici žádná pg_hba. conf pro hostitele `<IP address>` , uživatele" myadmin ", databázi" Postgres ", protokol SSL je povinný: připojení SSL je povinné. Zadejte možnosti SSL a zkuste to znovu. "
   >
   > Ověřte, že je v pravidlech brány firewall povolená IP adresa klienta.

2. Zadáním následujícího příkazu na příkazovém řádku vytvořte prázdnou databázi s názvem **postgresdb** :

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Na příkazovém řádku spusťte následující příkaz, který přepne připojení na nově vytvořenou databázi **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Zadejte  `\q` a vyberte Enter pro ukončení psql.

V této části jste se připojili k serveru Azure Database for PostgreSQL přes psql a vytvořili prázdnou uživatelskou databázi.

## <a name="reset-the-admin-password"></a>Resetování hesla správce

Heslo role správce můžete změnit pomocí následujícího příkazu:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Vyberte heslo, které má minimálně 8 znaků a maximálně 128 znaků. Heslo musí obsahovat znaky ze tří z následujících kategorií: 
> - Velká písmena anglické abecedy
> - Malá písmena anglické abecedy
> - Čísla
> - Jiné než alfanumerické znaky

## <a name="delete-a-server"></a>Odstranění serveru

Pokud chcete odstranit Azure Database for PostgreSQL flexibilní Server, spusťte příkaz [AZ Postgres Flexible-Server Delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

- [Vysvětlení konceptů zálohování a obnovení](concepts-backup-restore.md)
- [Vyladění a sledování serveru](concepts-monitoring.md)