---
title: Připojit k jednotce Google - Azure Logic Apps | Microsoft Docs
description: Vytvoření a správa souborů s Google Drive REST API a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4994687afbdd23f0265138cd1d4eff53bb47c163
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295879"
---
# <a name="get-started-with-the-google-drive-connector"></a>Začínáme s konektorem Google Drive
Připojte k Google jednotka pro vytvoření souborů, řádky get a další. Google Drive můžete: 

* Sestavení vaší firmy toku na základě dat, které můžete získat z hledání. 
* Použijte k hledání bitové kopie, vyhledávání zprávy a další akce. Tyto akce se odpověď a pak proveďte výstup k dispozici pro další akce. Můžete například hledat video a potom pomocí služby Twitter post, videa na Twitteru.

Můžete začít s vytvářením aplikace logiky teď najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Vytvoření připojení k Google Drive
Když přidáte tento konektor aplikace logiky, musíte je nejdříve autorizovat logic apps, abyste připojení k jednotce Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Po vytvoření připojení, je třeba zadat Google Drive vlastnosti, jako je název souboru nebo cestu složky. 

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/googledrive/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).
