---
title: 'Kurz: vytváření Azure Database for PostgreSQL – flexibilní serverovou a Azure App Serviceovou webovou aplikaci ve stejné virtuální síti'
description: Průvodce rychlým startem pro vytvoření Azure Database for PostgreSQL-flexibilního serveru s webovou aplikací ve virtuální síti
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ab606e357bd911f4d7f266977bd14871f92744a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546564"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Kurz: vytvoření Azure Database for PostgreSQL flexibilního serveru s webovou aplikací App Services ve virtuální síti

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci v Azure App Service s Azure Database for PostgreSQLm flexibilním serverem (ve verzi Preview) ve [virtuální síti](../../virtual-network/virtual-networks-overview.md).

V tomto kurzu budete
>[!div class="checklist"]
> * Vytvoření PostgreSQL flexibilního serveru ve virtuální síti
> * Vytvoření webové aplikace
> * Přidání webové aplikace do virtuální sítě
> * Připojení k Postgres z webové aplikace 

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) . Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Vytvoření PostgreSQL flexibilního serveru v nové virtuální síti

Pomocí následujícího příkazu vytvořte privátní server v rámci virtuální sítě (VNET):
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte skupinu prostředků, pokud ještě neexistuje.
- Vygeneruje název serveru, pokud není k dispozici.
- Vytvořte novou virtuální síť pro nový server postgreSQL. Poznamenejte si název virtuální sítě a název podsítě vytvořené pro váš server, protože potřebujete přidat webovou aplikaci ke stejné virtuální síti.
- Vytvoří uživatelské jméno správce a heslo pro váš server, pokud není k dispozici.
- Vytvoří prázdnou databázi s názvem **Postgres** .

> [!NOTE]
> - Poznamenejte si heslo, které vám bude vygenerováno, pokud není k dispozici. Pokud zapomenete heslo, museli byste heslo resetovat pomocí příkazu. ``` az postgres flexible-server update```
> - Pokud App Service Environment nepoužíváte, budete muset pomocí tohoto příkazu povolit přístup z libovolných IP adres Azure. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>Vytvoření webové aplikace
V této části vytvoříte hostitele aplikací v aplikaci App Service, připojíte tuto aplikaci k databázi Postgres a potom do tohoto hostitele nasadíte svůj kód. Ujistěte se, že jste v terminálu kořenový adresář úložiště kódu vaší aplikace.

Vytvoření aplikace App Service (hostitelský proces) pomocí příkazu AZ WebApp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - Pro argument--Location použijte stejné umístění jako u databáze v předchozí části.
> - V rámci všech Azure nahraďte <název aplikace> jedinečným názvem (koncový bod serveru je https:// \<app-name> . azurewebsites.NET). Povolené znaky pro <App-Name> jsou a-Z, 0-9 a-. Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.

Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte skupinu prostředků, pokud ještě neexistuje. (V tomto příkazu použijete stejnou skupinu prostředků, ve které jste databázi vytvořili dříve.)
- ```testappserviceplan```Pokud neexistuje, vytvořte plán App Service v cenové úrovni Basic (B1). --plán a--SKU jsou volitelné.
- Pokud neexistuje, vytvořte aplikaci App Service.
- Povolí výchozí protokolování pro aplikaci, pokud ještě není povolené.
- Nahrajte úložiště pomocí nasazení ZIP s povoleným automatizací buildu.

## <a name="add-the-web-app-to-the-virtual-network"></a>Přidání webové aplikace do virtuální sítě
Pomocí příkazu **AZ WebApp VNet-Integration** přidejte integraci místní virtuální sítě do WebApp. Nahraďte <název sítě VNet> a <název podsítě> s virtuální sítí a názvem podsítě, kterou flexibilní Server používá.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurace proměnných prostředí pro připojení databáze
S kódem teď nasazeným do App Service je dalším krokem připojení aplikace k flexibilnímu serveru v Azure. Kód aplikace očekává nalezení informací o databázi v řadě proměnných prostředí. Chcete-li nastavit proměnné prostředí v App Service, vytvořte pomocí příkazu Set "App Settings" ```az webapp config appsettings``` .

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Nahraďte ```postgres-server-name``` , ```username``` ```password``` pro nově vytvořený flexibilní serverový příkaz.
- Nahraďte <username> a <password> s přihlašovacími údaji, které příkaz vygeneroval také.
- Název skupiny prostředků a aplikace se vykreslí z hodnot uložených v mezipaměti v souboru. Azure/config.
- Příkaz vytvoří nastavení s názvem ```DBHOST``` , ```DBNAME``` , ```DBUSER``` a ```DBPASS``` . Pokud kód aplikace používá jiný název pro informace o databázi, pak použijte tyto názvy pro nastavení aplikace, jak je uvedeno v kódu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky, které jste vytvořili v tomto kurzu, vyčistěte pomocí následujícího příkazu. Tento příkaz odstraní všechny prostředky v této skupině prostředků.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)