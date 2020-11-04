---
title: 'Rychlý Start: vytvoření serveru – Azure CLI – Azure Database for MySQL'
description: Tento rychlý start popisuje, jak použít rozhraní příkazového řádku Azure k vytvoření serveru Azure Database for MySQL ve skupině prostředků Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0c43f1ea81bbc29817e6a2509c9967bf3b5782c9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337281"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Rychlý Start: vytvoření serveru Azure Database for MySQL pomocí Azure CLI

> [!TIP]
> Zvažte použití jednoduššího příkazu [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI (aktuálně ve verzi Preview). Vyzkoušejte si [rychlý Start](./quickstart-create-server-up-azure-cli.md).

V tomto rychlém startu se dozvíte, jak pomocí příkazů rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) v [Azure Cloud Shell](https://shell.azure.com) vytvořit Azure Database for MySQL server během pěti minut. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Předpoklady
Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure. 

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [AZ Group Create](/cli/azure/group) a pak vytvořte svůj server MySQL v této skupině prostředků. Měli byste zadat jedinečný název. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Vytvořte server Azure Database for MySQL pomocí příkazu [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create). Server může obsahovat více databází.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Zde jsou uvedeny podrobnosti o argumentech výše: 

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zadejte jedinečný název serveru Azure Database for MySQL. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
location | westus | Lokace Azure pro server.
admin-user | myadmin | Uživatelské jméno pro přihlášení správce. Nemůže být **azure_superuser** , **admin** , **administrátor** , **root** , **host** nebo **public**.
admin-password | *bezpečné heslo* | Heslo uživatele, který je správcem. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.
sku-name|GP_Gen5_2|Zadejte název cenové úrovně a konfiguraci výpočtů. Postupuje podle konvence {cenové úrovně} _{COMPUTE_ } {virtuální jádra} ve zkráceném znění. Další informace najdete v [cenové úrovni](./concepts-pricing-tiers.md) .

>[!IMPORTANT] 
>- Výchozí verze MySQL na serveru je 5,7. V současné době jsou k dispozici i verze 5,6 a 8,0.
>- Chcete-li zobrazit všechny argumenty příkazu **AZ MySQL server Create** , přečtěte si tento [referenční dokument](/cli/azure/mysql/server#az-mysql-server-create).
>- Protokol SSL je ve výchozím nastavení povolen na vašem serveru. Další infroamtion na SSL najdete v tématu [Konfigurace připojení SSL](howto-configure-ssl.md) .

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru 
Ve výchozím nastavení je nově vytvořený server chráněný pomocí pravidel brány firewall a není veřejně přístupný. Pravidlo brány firewall můžete nakonfigurovat na serveru pomocí příkazu [AZ MySQL server firewall-Rule Create](/cli/azure/mysql/server/firewall-rule) . To vám umožní připojit se k místnímu serveru.

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte IP adresu, ze které se budete připojovat. V případě potřeby můžete použít rozsah IP adres. Nevíte, jak hledat vaši IP adresu, a pak přejít na [https://whatismyipaddress.com/](https://whatismyipaddress.com/) adresu a získat IP adresu.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si **fullyQualifiedDomainName** a **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Připojení k Azure Database for MySQL serveru pomocí klienta příkazového řádku MySQL
K serveru se můžete připojit pomocí oblíbeného nástroje klienta, **[mysql.exe](https://dev.mysql.com/downloads/)** nástroje příkazového řádku s [Azure Cloud Shell](../cloud-shell/overview.md). Alternativně můžete použít příkazový řádek MySQL v místním prostředí.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tyto prostředky nepotřebujete pro další rychlý start nebo kurz, můžete je pomocí následujícího příkazu odstranit: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete odstranit jenom nově vytvořený server, můžete spustit příkaz [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Vytvoření aplikace v PHP ve Windows pomocí MySQL](../app-service/tutorial-php-mysql-app.md)
