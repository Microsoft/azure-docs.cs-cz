---
title: Připojení k síti Facebook - Azure Logic Apps | Microsoft Docs
description: Spravovat časového harmonogramu a stránka s rozhraní REST API sítě Facebook a Azure Logic Apps
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
ms.openlocfilehash: 985f3cf70a07b3080f34181e64c5bb1419d530bd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295002"
---
# <a name="get-started-with-the-facebook-connector"></a>Začínáme s konektorem služby Facebook
Připojení k síti Facebook a příspěvků na timeline, získat kanálů stránek a další. Facebook můžete:

* Sestavení vaší firmy toku na základě dat, které máte ze sítě Facebook. 
* Používejte aktivační událost, když je obdržena nového příspěvku.
* Použití akce, které odeslání na časové ose získat kanálů stránek a další. Tyto akce se odpověď a pak proveďte výstup k dispozici pro další akce. Například po nového příspěvku na časové ose můžete provést tento post a push na váš informační kanál sítě Twitter. 

Můžete začít s vytvářením aplikace logiky teď najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Umožňuje vytvořit připojení k síti Facebook
Když přidáte tento konektor aplikace logiky, musíte je nejdříve autorizovat logic apps, abyste připojení k vaší službě Facebook.

1. Přihlaste se k účtu služby Facebook
2. Vyberte **Authorize**a povolit logic apps, abyste připojit a používat vaší sítě Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/facebook/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).