---
title: Monitorování událostí Azure Media Services pomocí Event Grid pomocí rozhraní příkazového řádku | Microsoft Docs
description: Tento článek popisuje, jak se přihlásit k odběru Event Grid, aby bylo možné monitorovat události Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937287"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Vytváření a sledování událostí Media Services pomocí Event Grid pomocí Azure CLI

Azure Event Grid je služba Eventing Service pro Cloud. Tato služba používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí předplatitelům. Media Services události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost Media Services, protože vlastnost eventType začíná na "Microsoft. Media". Další informace najdete v tématu [Media Services schématech událostí](media-services-event-schemas.md).

V tomto článku se pomocí Azure CLI přihlásíte k odběru událostí pro váš účet Azure Media Services. Potom můžete aktivovat události pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který zpracovává data události a provádí akce. V tomto článku odesíláte události do webové aplikace, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- Nainstalujte a použijte rozhraní příkazového řádku místně. Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit verzi, kterou máte, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době nejsou v Azure Cloud Shell správné příkazy rozhraní příkazového [řádku Media Services V3](https://aka.ms/ams-v3-cli-ref) . Doporučuje se používat rozhraní příkazového řádku místně.

- [Vytvořte účet Media Services](create-account-cli-how-to.md).

    Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zprávy

Před přihlášením k odběru událostí pro Media Services účet vytvoříme koncový bod pro zprávu události. Koncový bod obvykle provádí akce na základě dat události. V tomto článku nasadíte [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán App Service, App Service webovou aplikaci a zdrojový kód z GitHubu.

1. Vyberte **nasadit do Azure** a nasaďte řešení do svého předplatného. V Azure Portal zadejte hodnoty parametrů.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení se podívejte na webovou aplikaci a ujistěte se, že je spuštěná. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přepnete na web "Azure Event Grid Viewer", uvidíte, že zatím neobsahuje žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu zadejte ID předplatného Azure, které chcete použít pro účet Media Services. Seznam předplatných, ke kterým máte přístup, můžete zobrazit tak, že přejdete na [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Přihlášení k odběru Media Servicesch událostí

Přihlásíte se k odběru článku, který informuje Event Grid události, které chcete sledovat. V následujícím příkladu se přihlásí k odběru účtu Media Services, který jste vytvořili, a předá adresu URL z webu, který jste vytvořili jako koncový bod pro oznamování událostí. 

Nahraďte `<event_subscription_name>` jedinečným názvem pro odběr události. U `<resource_group_name>` a `<ams_account_name>` použijte hodnoty, které jste použili při vytváření účtu Media Services. Pro `<endpoint_URL>` zadejte adresu URL webové aplikace a přidejte `api/updates` k adrese URL domovské stránky. Když při přihlášení k odběru zadáte koncový bod, Event Grid zpracuje směrování událostí do tohoto koncového bodu. 

1. Získat ID prostředku

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Příklad:

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

    Příklad:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Můžete obdržet upozornění ověření handshake. Dejte mu několik minut a Metoda handshake by měla ověřit.

Teď spustíme události, které vám pomůžou zjistit, jak Event Grid distribuuje zprávu do koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Události pro účet Media Services můžete aktivovat spuštěním úlohy kódování. Můžete postupovat podle [tohoto rychlého](stream-files-dotnet-quickstart.md) startu a zakódovat soubor a začít odesílat události. 

Znovu zobrazte svou webovou aplikaci a Všimněte si, že jí byla odeslána událost ověření předplatného. Event Grid odešle událost ověření, aby koncový bod mohl ověřit, zda chce přijímat data události. Koncový bod musí nastavit `validationResponse` na `validationCode`. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace, abyste viděli, jak ověřuje předplatné.

> [!TIP]
> Výběrem ikony oka rozbalte data události. Neaktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazit událost předplatného](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)

