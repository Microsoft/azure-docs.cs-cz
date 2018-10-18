---
title: Sledování událostí služby Azure Media Services pomocí služby Event Grid pomocí portálu | Dokumentace Microsoftu
description: Tento článek ukazuje, jak se přihlásit k odběru Event gridu kvůli monitorování události služby Azure Media Services.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 4e6527bf115f327635a0b0fe187094dafb320598
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380965"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Vytvořit a monitorovat události služby Media Services pomocí služby Event Grid pomocí webu Azure portal

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku použijete pro přihlášení k odběru událostí účtu Azure Media Services na webu Azure portal. Pak spustí událostí, abyste viděli výsledek. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V tomto článku budeme události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

## <a name="prerequisites"></a>Požadavky 

* Máte aktivní předplatné Azure.
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Než se přihlásíte k odběru událostí účtu Media Services, vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. V tomto článku nasadíte [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Pokud přejdete na web "Prohlížeč Azure Event Grid", uvidíte, že ještě nemá žádné události.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Přihlášení k odběru událostí služby Media Services

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Na portálu vyberte svůj účet Media Services a vyberte **události**.
1. Pokud chcete do aplikace prohlížeče odesílat události, použijte jako koncový bod webhook. 

   ![Výběr webhooku](./media/monitor-events-portal/select-web-hook.png)

1. Odběr událostí je předem hodnotami pro váš účet Media Services. 
1. Vyberte "Webhook" pro **typ koncového bodu**.
1. V tomto tématu nezpracováváme a necháváme **odběru pro všechny typy událostí** zaškrtnuto. Však můžete zrušit zaškrtnutí a filtr pro určité typy událostí. 
1. Klikněte na **vyberte koncový bod** odkaz.

    Jako koncový bod webhooku zadejte adresu URL vaší webové aplikace a do adresy URL domovské stránky přidejte `api/updates`. 

1. Stisknutím klávesy **potvrďte výběr**.
1. Stiskněte **Vytvořit**.
1. Zadejte název vašeho předplatného.

   ![Výběr protokolů](./media/monitor-events-portal/create-subscription.png)

1. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. 

    Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Koncový bod má nastavení `validationResponse` k `validationCode`. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](../../event-grid/security-authentication.md). Můžete zobrazit kód webové aplikace, které chcete zobrazit, jak ho ověří předplatné.

Nyní aktivujeme událostí naleznete v tématu jak Služba Event Grid distribuuje zprávu do vašeho koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Spuštěním úlohy kódování, mohou aktivovat události pro účet Media Services. Můžete postupovat podle [v tomto rychlém startu](stream-files-dotnet-quickstart.md) kódování souboru a zahájit odesílání událostí. Pokud odebíráte ke všem událostem, zobrazí se obrazovka podobná této:

> [!TIP]
> Vyberte ikonu oka a rozbalte data události. Není vhodné aktualizujte stránku, pokud chcete zobrazit všechny události.

![Zobrazení události odběru](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování a streamování](stream-files-tutorial-with-api.md)
