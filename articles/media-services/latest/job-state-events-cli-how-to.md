---
title: Monitorování událostí Azure Media Services pomocí Event Grid pomocí rozhraní příkazového řádku | Microsoft Docs
description: Tento článek popisuje, jak se přihlásit k odběru Event Grid, aby bylo možné monitorovat Azure Media Services události pomocí Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 072bfb22eba82d7a39d985f72cbc78c0639a4795
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976833"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Vytváření a sledování událostí Media Services pomocí Event Grid pomocí Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid je služba zpracování událostí pro cloud. Tato služba používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí předplatitelům. Media Services události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost Media Services, protože vlastnost eventType začíná na "Microsoft. Media". Další informace najdete v tématu [Media Services schématech událostí](media-services-event-schemas.md).

V tomto článku se pomocí Azure CLI přihlásíte k odběru událostí pro váš účet Azure Media Services. Potom můžete aktivovat události pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V tomto článku odesíláte události do webové aplikace, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Požadavky

- Musíte mít aktivní předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Nainstalujte a použijte rozhraní příkazového řádku místně. Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

    V současné době nejsou v Azure Cloud Shell správné příkazy rozhraní příkazového [řádku Media Services V3](https://aka.ms/ams-v3-cli-ref) . Doporučuje se používat rozhraní příkazového řádku místně.

- [Vytvořte účet Media Services](./create-account-howto.md).

    Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí pro Media Services účet vytvoříme koncový bod pro zprávu události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přepnete na web "Azure Event Grid Viewer", uvidíte, že zatím neobsahuje žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Přihlášení k odběru Media Servicesch událostí

Přihlásíte se k odběru článku, který informuje Event Grid události, které chcete sledovat. V následujícím příkladu se přihlásí k odběru účtu Media Services, který jste vytvořili, a předá adresu URL z webu, který jste vytvořili jako koncový bod pro oznamování událostí. 

Nahraďte `<event_subscription_name>` jedinečným názvem pro odběr události. V případě `<resource_group_name>` a `<ams_account_name>` použijte hodnoty, které jste použili při vytváření účtu Media Services. V případě `<endpoint_URL>` Zadejte adresu URL webové aplikace a přidejte `api/updates` ji na domovskou stránku URL. Když při přihlášení k odběru zadáte koncový bod, Event Grid zpracuje směrování událostí do tohoto koncového bodu. 

1. Získat ID prostředku

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Například:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Přihlášení k odběru událostí

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Například:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Můžete obdržet upozornění ověření handshake. Dejte mu několik minut a Metoda handshake by měla ověřit.

Teď spustíme události, které vám pomůžou zjistit, jak Event Grid distribuuje zprávu do koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Události pro účet Media Services můžete aktivovat spuštěním úlohy kódování. Můžete postupovat podle [tohoto rychlého](stream-files-dotnet-quickstart.md) startu a zakódovat soubor a začít odesílat události. 

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod musí být nastaven `validationResponse` na hodnotu `validationCode` . Další informace najdete v tématu [Event Grid zabezpečení a ověřování](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace, abyste viděli, jak ověřuje předplatné.

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Neaktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)
