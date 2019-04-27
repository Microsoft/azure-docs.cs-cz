---
title: Připojte se k Office 365 Video – Azure Logic Apps | Dokumentace Microsoftu
description: Spravovat videa s využitím rozhraní API REST pro Office 365 Video a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c10a2aa097b63fd3751be01bbfeb6097080bbb9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105815"
---
# <a name="get-started-with-the-office365-video-connector"></a>Začínáme s konektorem Office 365 Video
Připojte se k Office 365 Video na získání informací o Office 365 video, získání seznamu videa a další. Office 365 Video můžete:

* Vytvoření obchodní toku na základě dat, který jste získali z Office 365 Video. 
* Pomocí akcí, které zkontrolovat stav, získání seznamu všech videa v kanálu a další. Tyto akce získat odpověď a poté zpřístupní výstup pro další akce. Můžete například použít konektor vyhledávání Bingu pro vyhledávání videí Office 365 a potom pomocí konektoru Office 365 video a získat informace o tomto videu. Pokud video vyhovuje vašim požadavkům, můžete toto video příspěvku na Facebooku. 

Můžete začít tak, že teď vytvoříte aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Vytvořte připojení ke konektoru Office 365 Video
Při přidání tohoto konektoru logic Apps musíte přihlásit ke svému účtu Office 365 Video a povolit aplikacím logiky připojíte ke svému účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Po vytvoření připojení, zadejte vlastnosti Office 365 video, jako je název tenanta nebo ID kanálu 


## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).