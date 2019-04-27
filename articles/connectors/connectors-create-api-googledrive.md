---
title: Připojte se k disku Google – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření a správa souborů pomocí služby Google Drive rozhraní REST API a Azure Logic Apps
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
ms.openlocfilehash: 43bd5248f1bb80c71a85935c585deac6152be78b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105085"
---
# <a name="get-started-with-the-google-drive-connector"></a>Začínáme s konektorem disku Google.
Připojte se k disku Google vytvořit soubory, získat řádky a další. Disk Google můžete: 

* Vytvoření obchodní toku na základě dat, který jste získali z vašeho hledání. 
* Pomocí akcí k hledání obrázků, vyhledávání novinky a další. Tyto akce získat odpověď a poté zpřístupní výstup pro další akce. Můžete například vyhledat nějaké video a pak použít k publikování, který videa na twitteru přes Twitter.

Můžete začít tak, že teď vytvoříte aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Vytvoření připojení k disku Google.
Při přidání tohoto konektoru logic Apps, musíte se autorizovat aplikace logiky se připojit k disku Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Po vytvoření připojení, zadejte vlastnosti disku Google, jako je název cesty nebo souboru složky. 

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/googledrive/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).
