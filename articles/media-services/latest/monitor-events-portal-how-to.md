---
title: Sledování událostí Mediálních služeb Azure pomocí Gridu událostí pomocí portálu
description: Tento článek ukazuje, jak se přihlásit k odběru Event Grid za účelem sledování událostí Azure Media Services.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509219"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Vytvoření a monitorování událostí Media Services se službou Event Grid s využitím webu Azure Portal

Azure Event Grid je služba zpracování událostí pro cloud. Tato služba používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Události služby Media Services obsahují všechny informace, které potřebujete k reakci na změny dat. Událost Služby Media Services můžete identifikovat, protože vlastnost eventType začíná na "Microsoft.Media". Další informace naleznete v [tématu Schémata událostí služby Media Services](media-services-event-schemas.md).

V tomto článku se pomocí portálu Azure přihlásíte k odběru událostí pro váš účet Azure Media Services. Potom spustíte události pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V článku odesíláme události do webové aplikace, která shromažďuje a zobrazuje zprávy.

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

## <a name="prerequisites"></a>Požadavky 

* Mít aktivní předplatné Azure.
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí pro účet Media Services vytvořme koncový bod pro zprávu o události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem sestavenou webovou aplikaci,](https://github.com/Azure-Samples/azure-event-grid-viewer) která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přepnete na web "Azure Event Grid Viewer", uvidíte, že zatím nemá žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Přihlásit se k odběru událostí Media Services

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Na portálu vyberte účet Mediálních služeb a **vyberte Události**.
1. Pokud chcete do aplikace prohlížeče odesílat události, použijte jako koncový bod webhook. 

   ![Výběr webhooku](./media/monitor-events-portal/select-web-hook.png)

1. Odběr událostí je předvyplněn o hodnoty pro váš účet mediálních služeb. 
1. Vyberte možnost "Webový hák" pro **typ koncového bodu**.
1. V tomto tématu ponecháme **zaškrtnuté políčko Přihlásit se ke všem typům událostí.** Můžete ji však zrušit a filtrovat pro konkrétní typy událostí. 
1. Klikněte na odkaz **Vybrat koncový bod.**

    Jako koncový bod webhooku zadejte adresu URL vaší webové aplikace a do adresy URL domovské stránky přidejte `api/updates`. 

1. Stiskněte **tlačítko Potvrdit výběr**.
1. Stiskněte **Vytvořit**.
1. Zadejte název vašeho předplatného.

   ![Výběr protokolů](./media/monitor-events-portal/create-subscription.png)

1. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. 

    Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod musí `validationResponse` být `validationCode`nastaven na . Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace a zjistit, jak ověřuje předplatné.

Nyní aktivujeme události a zobrazíse, jak event grid distribuuje zprávu do koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Události pro účet Mediální služby můžete spustit spuštěním úlohy kódování. Podle [tohoto rychlého startu](stream-files-dotnet-quickstart.md) můžete kódovat soubor a začít odesílat události. Pokud jste se přihlásili k odběru všech událostí, zobrazí se obrazovka podobná následující:

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Neaktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)
