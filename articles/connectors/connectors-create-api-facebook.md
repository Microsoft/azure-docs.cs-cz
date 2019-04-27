---
title: Připojení k Facebooku – Azure Logic Apps | Dokumentace Microsoftu
description: Umožňuje spravovat vaše časové osy a stránku Facebooku REST API a Azure Logic Apps
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
ms.openlocfilehash: 25595127d913d3cd093e0af3d7916e33fc7cb352
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105970"
---
# <a name="get-started-with-the-facebook-connector"></a>Začínáme s konektor pro Facebook
Připojení k Facebooku a příspěvků na timeline, získání kanálů stránek a dalších akcí. Pomocí Facebooku můžete:

* Vytvoření obchodní toku na základě dat, které získáte ze sítě Facebook. 
* Pomocí aktivační události při přijetí nového příspěvku.
* Použití akce, které příspěvek na vaší časové ose, získejte kanálů stránek a mnoho dalšího. Tyto akce získat odpověď a poté zpřístupní výstup pro další akce. Například po nový příspěvek na vaší časové ose můžete využít tohoto příspěvku a push na váš informační kanál Twitteru. 

Můžete začít tak, že teď vytvoříte aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Vytvoření připojení k Facebooku
Při přidání tohoto konektoru logic Apps, musíte se autorizovat logic apps pro připojení k vaší službě Facebook.

1. Přihlaste se ke svému Facebookovému účtu
2. Vyberte **Authorize**a povolit aplikace logiky pro připojení a použití vaší sítě Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/facebook/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).