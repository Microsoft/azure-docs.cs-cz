---
title: Sledování událostí služby Azure Media Services pomocí služby Event Grid pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Tento článek ukazuje, jak se přihlásit k odběru Event gridu kvůli monitorování události služby Azure Media Services.
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
ms.openlocfilehash: 16f964c6f881777e0217979a329610902b29a87b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612612"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Vytvořit a monitorovat události služby Media Services pomocí služby Event Grid pomocí Azure CLI

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí rozhraní příkazového řádku Azure k přihlášení k odběru událostí účtu Azure Media Services. Pak spustí událostí, abyste viděli výsledek. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V tomto článku budete události odesílat na webovou aplikaci, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

- [Vytvoření účtu Media Services](create-account-cli-how-to.md).

    Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Než se přihlásíte k odběru událostí účtu Media Services, vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přejdete na web "Prohlížeč Azure Event Grid", uvidíte, že ještě nemá žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

V následujícím příkazu uveďte ID předplatného Azure, které chcete pro účet Media Services použít. Když přejdete na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zobrazí se seznam předplatných, ke kterým máte přístup.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Přihlášení k odběru událostí služby Media Services

Se přihlásíte k odběru článku zjistit služby Event Grid, které události chcete sledovat. Následující příklad se přihlásí k účtu Media Services, které jste vytvořili a předá adresu URL z webu, který jste vytvořili jako koncový bod pro oznámení události. 

Nahraďte `<event_subscription_name>` s jedinečným názvem vašeho odběru událostí. Pro `<resource_group_name>` a `<ams_account_name>`, použijte hodnoty, které jste použili při vytváření účtu Media Services. Pro `<endpoint_URL>`, zadejte adresu URL webové aplikace a přidat `api/updates` na adresu URL domovské stránky. Při přihlášení k odběru, zadáte koncový bod, Služba Event Grid zpracovávat směrování událostí do tohoto koncového bodu. 

1. Získejte id prostředku

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Příklad:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Události odebírají

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Příklad:

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Může se zobrazit upozornění ověření metody handshake. Nabízí několik minut a signalizace by měl ověřit.

Nyní aktivujeme událostí naleznete v tématu jak Služba Event Grid distribuuje zprávu do vašeho koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Spuštěním úlohy kódování, mohou aktivovat události pro účet Media Services. Můžete postupovat podle [v tomto rychlém startu](stream-files-dotnet-quickstart.md) kódování souboru a zahájit odesílání událostí. 

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod má nastavení `validationResponse` k `validationCode`. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace, které chcete zobrazit, jak ho ověří předplatné.

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Není vhodné aktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)

