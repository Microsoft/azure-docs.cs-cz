---
title: 'Kurz: vytvoření Azure Database for MySQL flexibilního serveru (Preview) a Azure App Service webové aplikace ve stejné virtuální síti'
description: Průvodce rychlým startem pro vytváření Azure Database for MySQL flexibilního serveru (Preview) s webovou aplikací ve virtuální síti
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770016"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Kurz: vytvoření Azure Database for MySQL-flexibilního serveru (ve verzi Preview) pomocí App Services webové aplikace ve virtuální síti

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci v Azure App Service pomocí MySQL flexibilního serveru (Preview) ve [virtuální síti](../../virtual-network/virtual-networks-overview.md).

V tomto kurzu se naučíte, jak:
>[!div class="checklist"]
> * Vytvoření flexibilního serveru MySQL ve virtuální síti
> * Vytvořte podsíť, která bude delegována na App Service
> * Vytvoření webové aplikace
> * Přidání webové aplikace do virtuální sítě
> * Připojení k Postgres z webové aplikace 

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az_login) . Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Vytvoření Azure Database for MySQL flexibilního serveru

Pomocí následujícího příkazu vytvořte privátní server v rámci virtuální sítě (VNET):
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Zkopírujte připojovací řetězec a název nově vytvořené virtuální sítě. Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte skupinu prostředků, pokud ještě neexistuje.
- Vygeneruje název serveru, pokud není k dispozici.
- Vytvořte novou virtuální síť pro nový server MySQL. Poznamenejte si název virtuální sítě a název podsítě vytvořené pro váš server, protože potřebujete přidat webovou aplikaci ke stejné virtuální síti.
- Vytvoří uživatelské jméno správce a heslo pro váš server, pokud není k dispozici.
- Vytvoří prázdnou databázi s názvem **flexibleserverdb** .

> [!NOTE]
> Poznamenejte si heslo, které vám bude vygenerováno, pokud není k dispozici. Pokud zapomenete heslo, museli byste heslo resetovat pomocí příkazu. ``` az mysql flexible-server update```

## <a name="create-subnet-for-app-service-endpoint"></a>Vytvoření podsítě pro App Service koncový bod
Teď je potřeba mít podsíť, která je delegovaná na App Service koncový bod webové aplikace. Spuštěním následujícího příkazu vytvořte novou podsíť ve stejné virtuální síti, jakou vytvořil databázový server. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Poznamenejte si název virtuální sítě a název podsítě za tímto příkazem, protože by ho musel přidat pravidlo integrace virtuální sítě pro webovou aplikaci po jeho vytvoření. 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V této části vytvoříte hostitele aplikací v aplikaci App Service a připojíte tuto aplikaci k databázi MySQL. Ujistěte se, že jste v terminálu kořenový adresář úložiště kódu vaší aplikace.

Vytvoření aplikace App Service (hostitelský proces) pomocí příkazu AZ WebApp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Pro argument--Location použijte stejné umístění jako u databáze v předchozí části.
> - V rámci všech Azure nahraďte _&lt;>App-Name_ jedinečným názvem (koncový bod serveru je https:// \<app-name> . azurewebsites.NET). Povolené znaky pro <App-Name> jsou a-Z, 0-9 a-. Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
> - Úroveň Basic App Service nepodporuje integraci virtuální sítě. Použijte prosím Standard nebo Premium. 

Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte skupinu prostředků, pokud ještě neexistuje. (V tomto příkazu použijete stejnou skupinu prostředků, ve které jste databázi vytvořili dříve.)
- ```testappserviceplan```Pokud neexistuje, vytvořte plán App Service v cenové úrovni Basic (B1). --plán a--SKU jsou volitelné.
- Pokud neexistuje, vytvořte aplikaci App Service.
- Povolí výchozí protokolování pro aplikaci, pokud ještě není povolené.
- Nahrajte úložiště pomocí nasazení ZIP s povoleným automatizací buildu.

## <a name="add-the-web-app-to-the-virtual-network"></a>Přidání webové aplikace do virtuální sítě

Pomocí příkazu **AZ WebApp VNet-Integration** přidejte integraci místní virtuální sítě do WebApp. Nahraďte _&lt; název VNet-Name>_ a _&lt; název podsítě_ názvem virtuální sítě a podsítě, kterou používá flexibilní Server.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurace proměnných prostředí pro připojení databáze

S kódem teď nasazeným do App Service je dalším krokem připojení aplikace k flexibilnímu serveru v Azure. Kód aplikace očekává nalezení informací o databázi v řadě proměnných prostředí. Chcete-li nastavit proměnné prostředí v App Service, vytvořte pomocí příkazu Set "App Settings" ```az webapp config appsettings``` .

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- U nově vytvořeného flexibilního serveru nahraďte položku _&lt; MySQL-Server-Name>_, _&lt; username>_ a _&lt; Password>_ .
- Nahraďte _&lt; uživatelské jméno>_ a _&lt; heslo>_ přihlašovacími údaji, které příkaz vygeneroval také.
- Název skupiny prostředků a aplikace se vykreslí z hodnot uložených v mezipaměti v souboru. Azure/config.
- Příkaz vytvoří nastavení s názvem DBHOST, DBNAME, DBUSER a DBPASS. Pokud kód aplikace používá jiný název pro informace o databázi, pak použijte tyto názvy pro nastavení aplikace, jak je uvedeno v kódu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky, které jste vytvořili v tomto kurzu, vyčistěte pomocí následujícího příkazu. Tento příkaz odstraní všechny prostředky v této skupině prostředků.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Mapování existujícího vlastního názvu DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
