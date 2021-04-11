---
title: Monitorování událostí Media Services pomocí portálu Event Grid
description: Tento článek popisuje, jak se přihlásit k odběru Event Grid, aby bylo možné monitorovat události Azure Media Services.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 1e288e4490916a7ee03371baa8e7cc3b798e272b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961706"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Vytvoření a monitorování událostí Media Services se službou Event Grid s využitím webu Azure Portal

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Azure Event Grid je služba zpracování událostí pro cloud. Tato služba používá [odběry událostí](../../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí předplatitelům. Media Services události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost Media Services, protože vlastnost eventType začíná na "Microsoft. Media". Další informace najdete v tématu [Media Services schématech událostí](media-services-event-schemas.md).

V tomto článku použijete Azure Portal k přihlášení k odběru událostí pro účet Azure Media Services. Potom můžete aktivovat události pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V článku odesílajíme události do webové aplikace, která shromažďuje a zobrazuje zprávy.

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

## <a name="prerequisites"></a>Požadavky 

* Musíte mít aktivní předplatné Azure.
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](../account-create-how-to.md)

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí pro Media Services účet vytvoříme koncový bod pro zprávu události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   [![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přepnete na web "Azure Event Grid Viewer", uvidíte, že zatím neobsahuje žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Přihlášení k odběru Media Servicesch událostí

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Na portálu vyberte účet Media Services a vyberte **události**.
1. Pokud chcete do aplikace prohlížeče odesílat události, použijte jako koncový bod webhook. 

   ![Výběr webhooku](../media/monitor-events-portal/select-web-hook.png)

1. Předplatné události je předem vyplněno hodnotami pro váš účet Media Services. 
1. Jako **Typ koncového bodu** vyberte Webhook.
1. V tomto tématu ponecháme **přihlášení k odběru všech zkontrolovaných typů událostí** . Můžete ho ale zrušit a vyfiltrovat pro konkrétní typy událostí. 
1. Klikněte na odkaz **Vybrat koncový bod** .

    Jako koncový bod webhooku zadejte adresu URL vaší webové aplikace a do adresy URL domovské stránky přidejte `api/updates`. 

1. Stiskněte **potvrdit výběr**.
1. Stiskněte **Vytvořit**.
1. Zadejte název vašeho předplatného.

   ![Výběr protokolů](../media/monitor-events-portal/create-subscription.png)

1. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. 

    Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod musí být nastaven `validationResponse` na hodnotu `validationCode` . Další informace najdete v tématu [Event Grid zabezpečení a ověřování](../../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace, abyste viděli, jak ověřuje předplatné.

Teď spustíme události, které vám pomůžou zjistit, jak Event Grid distribuuje zprávu do koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Události pro účet Media Services můžete aktivovat spuštěním úlohy kódování. Můžete postupovat podle [tohoto rychlého](../stream-files-dotnet-quickstart.md) startu a zakódovat soubor a začít odesílat události. Pokud se přihlásíte k odběru všech událostí, zobrazí se obrazovka podobná této:

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Neaktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](../media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování](../stream-files-tutorial-with-api.md)
