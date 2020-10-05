---
title: 'Rychlý Start: vytvoření serveru – Azure CLI-Azure Database for PostgreSQL-Single server'
description: V této příručce pro rychlý Start vytvoříte Azure Database for PostgreSQL Server pomocí rozhraní příkazového řádku Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: df7c27fb0aca6a9b903d29ea4414832fb677b3eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91705258"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Rychlý Start: vytvoření serveru Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte, jak pomocí příkazů rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v [Azure Cloud Shell](https://shell.azure.com) vytvořit jeden Azure Database for PostgreSQL Server během pěti minut. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!TIP]
> Zvažte použití jednoduššího příkazu [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI, který je aktuálně ve verzi Preview. Vyzkoušejte si [rychlý Start](./quickstart-create-server-up-azure-cli.md).

## <a name="prerequisites"></a>Předpoklady
Tento článek vyžaduje, abyste místně spustili Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Ke svému účtu se budete muset přihlásit pomocí příkazu [AZ Login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure. 

```azurecli-interactive
az login
```

Pomocí příkazu  [AZ Account set](/cli/azure/account) vyberte v rámci svého účtu konkrétní ID předplatného. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. 

```azurecli
az account set --subscription <subscription id>
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat všechna předplatná, použijte příkaz [AZ Account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) a pak vytvořte server PostgreSQL v této skupině prostředků. Měli byste zadat jedinečný název. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Pomocí příkazu [AZ Postgres Server Create](/cli/azure/postgres/server) vytvořte [Server Azure Database for PostgreSQL](overview.md) . Server může obsahovat více databází.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Zde jsou uvedeny podrobnosti o předchozích argumentech: 

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Jedinečný název, který identifikuje váš Azure Database for PostgreSQL Server. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí obsahovat 3 až 63 znaků.
resource-group | myresourcegroup | Název skupiny prostředků Azure.
location | westus | Umístění Azure pro server.
admin-user | myadmin | Uživatelské jméno pro přihlášení správce Nemůže to být **azure_superuser**, **admin**, **Administrator**, **root**, **Guest**ani **Public**.
admin-password | *bezpečné heslo* | Heslo uživatele správce. Musí obsahovat 8 až 128 znaků ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.
sku-name|GP_Gen5_2| Název cenové úrovně a konfigurace výpočtů. V zkrácených způsobech použijte konvenci {cenová úroveň}_{COMPUTE_} {virtuální jádra}. Další informace najdete v tématu [Azure Database for PostgreSQL ceny](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Výchozí verze PostgreSQL serveru je 9,6. Pokud chcete zobrazit všechny podporované verze, přečtěte si článek [podporované hlavní verze PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Pokud chcete zobrazit všechny argumenty příkazu **AZ Postgres Server Create** , přečtěte si [Tento referenční dokument](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- Protokol SSL je ve výchozím nastavení povolen na vašem serveru. Další informace o protokolu SSL najdete v tématu [Konfigurace připojení SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru 
Ve výchozím nastavení není server, který jste vytvořili, veřejně přístupný a je chráněný pomocí pravidel brány firewall. Pravidla brány firewall můžete na svém serveru nakonfigurovat pomocí příkazu [AZ Postgres server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) , který vašemu místnímu prostředí umožní přístup k serveru. 

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se budete připojovat. Pokud svou IP adresu neznáte, přečtěte si je na [whatismyipaddress.com](https://whatismyipaddress.com/) .


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Aby nedocházelo k problémům s připojením, ujistěte se, že brána firewall vaší sítě povoluje port 5432. Azure Database for PostgreSQL servery používají tento port. 

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, zadejte informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si hodnoty **administratorLogin** a **fullyQualifiedDomainName** .

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Připojení k Azure Database for PostgreSQL serveru pomocí psql
Klient [psql](https://www.postgresql.org/docs/current/static/app-psql.html) je oblíbená volba pro připojení k serverům PostgreSQL. K serveru se můžete připojit pomocí psql s [Azure Cloud Shell](../cloud-shell/overview.md). Pokud máte k dispozici psql, můžete použít i v místním prostředí. Prázdná databáze **Postgres**se automaticky vytvoří pomocí nového serveru PostgreSQL. Tuto databázi můžete použít pro připojení k psql, jak je znázorněno v následujícím kódu. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Pokud upřednostňujete použití cesty URL pro připojení k Postgres, adresa URL kódování @ uživatelského jména pomocí `%40` . Například připojovací řetězec pro psql by byl:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tyto prostředky nepotřebujete pro další rychlý Start nebo kurz, můžete je odstranit spuštěním následujícího příkazu. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete jenom odstranit ten nově vytvořený server, můžete spustit příkaz [AZ Postgres Server Delete](/cli/azure/postgres/server) .

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
> 
> [Nasazení webové aplikace v Django s PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Připojení k aplikaci Node.JS](./connect-nodejs.md)

