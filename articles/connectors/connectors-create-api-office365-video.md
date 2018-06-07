---
title: Připojit k Office 365 Video - Azure Logic Apps | Microsoft Docs
description: Spravovat videa s Office 365 Video REST API a Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ef56159735654cd5cefbba99ee9cead42c81086b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610324"
---
# <a name="get-started-with-the-office365-video-connector"></a>Začínáme s konektor Office 365 Video
Připojte k Office 365 videu se získat informace o Office 365 videa, získat seznam videa a další. Video Office 365 můžete:

* Sestavení vaší firmy toku na základě dat, které můžete získat z Office 365 Video. 
* Pomocí akcí, které zkontrolujte video portálu stav, získat seznam všech video v kanálu a další. Tyto akce se odpověď a pak proveďte výstup k dispozici pro další akce. Můžete například použít konektor Bing vyhledávání pro vyhledávání služeb Office 365 videa a pak použít video konektor Office 365 a získat informace o tomto video. Pokud se na video vyhovovat vašim požadavkům, můžete toto video účtovat ve službě Facebook. 

Můžete začít s vytvářením aplikace logiky teď najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Umožňuje vytvořit připojení k Office 365 Video konektoru
Když přidáte tento konektor aplikace logiky, musíte přihlásit ke svému účtu Office 365 Video a povolit logic apps, abyste připojení k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Po vytvoření připojení, zadejte vlastnosti videa Office 365, jako je název klienta nebo kanálu ID. 


## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).