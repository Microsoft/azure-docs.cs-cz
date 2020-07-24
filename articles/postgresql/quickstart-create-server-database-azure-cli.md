---
title: 'Rychlý Start: vytvoření serveru – Azure CLI-Azure Database for PostgreSQL-Single server'
description: Průvodce rychlým startem pro vytvoření serveru Azure Database for PostgreSQL-Single pomocí Azure CLI (rozhraní příkazového řádku).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030133"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Rychlý Start: vytvoření serveru Azure Database for PostgreSQL-Single pomocí rozhraní příkazového řádku Azure

> [!TIP]
> Zvažte použití jednoduššího příkazu [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI (aktuálně ve verzi Preview). Vyzkoušejte si [rychlý Start](./quickstart-create-server-up-azure-cli.md).

V tomto rychlém startu se dozvíte, jak pomocí příkazů rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v [Azure Cloud Shell](https://shell.azure.com) vytvořit Azure Database for PostgreSQL Server během pěti minut.  Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Předpoklady
Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** vašeho účtu Azure. 

```azurecli-interactive
az login
```

Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) a pak vytvořte server PostgreSQL v této skupině prostředků. Měli byste zadat jedinečný název. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Vytvořte [server Azure Database for PostgreSQL](overview.md) pomocí příkazu [az postgres server create](/cli/azure/postgres/server). Server může obsahovat více databází.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Zde jsou uvedeny podrobnosti o argumentech výše: 

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zvolte jedinečný název serveru, který identifikuje váš server Azure Database for PostgreSQL. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
location | westus | Lokace Azure pro server.
admin-user | myadmin | Uživatelské jméno pro přihlášení správce. Nemůže být ** azure_superuser **, ** admin **, ** administrátor **, ** root **, ** host ** nebo ** public**.
admin-password | *bezpečné heslo* | Heslo uživatele, který je správcem. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.
sku-name|GP_Gen5_2|Zadejte název cenové úrovně a konfiguraci výpočtů. Postupuje podle konvence {cenové úrovně}_{COMPUTE_} {virtuální jádra} ve zkráceném znění. Další informace najdete v tématu [Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Výchozí verze PostgreSQL serveru je 9,6. [Tady](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)najdete všechny podporované verze.
>- Všechny argumenty příkazu **AZ Postgres Server Create** najdete v tomto [referenčním dokumentu](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) .
>- Protokol SSL je ve výchozím nastavení povolen na vašem serveru. Další infroamtion na SSL najdete v tématu [Konfigurace připojení SSL](./concepts-ssl-connection-security.md) .

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru 
Ve výchozím nastavení není vytvořený server veřejně přístupný a chráněný pomocí pravidel brány firewall. Můžete nakonfigurovat pravidlo brány firewall na serveru pomocí příkazu [AZ Postgres server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) a poskytnout tak přístup k místnímu prostředí pro připojení k serveru. 

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se budete připojovat.  Pokud si nejste jisti, jak vaše IP adresa Hledat, použijte k [https://whatismyipaddress.com/](https://whatismyipaddress.com/) získání IP adresy.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Ujistěte se prosím, že brána firewall vaší sítě umožňuje port 5432, který se používá Azure Database for PostgreSQL servery, aby se předešlo problémům s připojením. 

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si položky **administratorLogin** a **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Připojení k Azure Database for PostgreSQL serveru pomocí psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) je oblíbený klient, který se používá pro připojení k serverům PostgreSQL. K serveru se můžete připojit pomocí **psql** s [Azure Cloud Shell](../cloud-shell/overview.md). Případně můžete použít psql v místním prostředí, pokud ho máte k dispozici. Prázdná databáze: ' Postgres ' je již vytvořen s novým serverem PostgreSQL, který můžete použít pro připojení k psql, jak je znázorněno níže. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Pokud upřednostňujete použití cesty URL pro připojení k Postgres, adresa URL kódování @ uživatelského jména pomocí `%40` . Například připojovací řetězec pro psql by byl,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tyto prostředky nepotřebujete pro další rychlý Start nebo kurz, můžete je odstranit spuštěním následujícího příkazu: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete odstranit jenom nově vytvořený server, můžete spustit příkaz [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
> 
> [Nasazení webové aplikace v Django s PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Připojení k aplikaci Node.JS](./connect-nodejs.md)

