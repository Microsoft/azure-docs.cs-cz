---
title: Sledování událostí mediálních služeb Azure pomocí gridu událostí pomocí příkazového příkazového příkazu | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak se přihlásit k odběru Event Grid za účelem sledování událostí Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71937287"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Vytváření a monitorování událostí mediálních služeb pomocí služby Event Grid pomocí příkazového příkazového příkazu Azure

Azure Event Grid je služba zpracování událostí pro cloud. Tato služba používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Události služby Media Services obsahují všechny informace, které potřebujete k reakci na změny dat. Událost Služby Media Services můžete identifikovat, protože vlastnost eventType začíná na "Microsoft.Media". Další informace naleznete v [tématu Schémata událostí služby Media Services](media-services-event-schemas.md).

V tomto článku pomocí azure cli přihlásit k odběru událostí pro váš účet Azure Media Services. Potom spustíte události pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V tomto článku odešlete události do webové aplikace, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.
- Nainstalujte a použijte příkazcli místně, tento článek vyžaduje Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

    V současné době ne všechny [příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu Příkazové/příkazy mediálních služeb v3](https://aka.ms/ams-v3-cli-ref) fungují v prostředí Azure Cloud Shell. Doporučuje se používat CLI místně.

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).

    Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Mediální služby.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí pro účet Media Services vytvořme koncový bod pro zprávu o události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem sestavenou webovou aplikaci,](https://github.com/Azure-Samples/azure-event-grid-viewer) která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přepnete na web "Azure Event Grid Viewer", uvidíte, že zatím nemá žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Přihlásit se k odběru událostí Media Services

Přihlášením se k odběru článku sdělíte aplikaci Event Grid, které události chcete sledovat. Následující příklad se přihlásí k odběru účtu Mediální služby, který jste vytvořili, a předá adresu URL z webu, který jste vytvořili jako koncový bod pro oznámení události. 

Nahraďte `<event_subscription_name>` jedinečnýnázev pro odběr událostí. For `<resource_group_name>` `<ams_account_name>`a , použijte hodnoty, které jste použili při vytváření účtu Mediálních služeb. V `<endpoint_URL>`aplikaci zadejte adresu URL `api/updates` webové aplikace a přidejte ji na adresu URL domovské stránky. Zadáním koncového bodu při přihlášení, Event Grid zpracovává směrování událostí do tohoto koncového bodu. 

1. Získání ID prostředku

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Například:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Přihlásit se k odběru akcí

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
    > Může se stát, že se vám ověření potřesení rukou upozornění. Dejte mu pár minut a podání ruky by mělo ověřit.

Nyní aktivujeme události a zobrazíse, jak event grid distribuuje zprávu do koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Události pro účet Mediální služby můžete spustit spuštěním úlohy kódování. Podle [tohoto rychlého startu](stream-files-dotnet-quickstart.md) můžete kódovat soubor a začít odesílat události. 

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod musí `validationResponse` být `validationCode`nastaven na . Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace a zjistit, jak ověřuje předplatné.

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Neaktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)

