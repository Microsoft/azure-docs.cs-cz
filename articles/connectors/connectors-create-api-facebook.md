---
title: Připojení k Facebooku – Azure Logic Apps
description: Správa časové osy a stránky pomocí rozhraní REST API pro Facebook a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050950"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Správa časové osy a stránky Facebooku pomocí Azure Logic Apps

Připojení k Facebooku a odeslání na časovou osu, získání informačního kanálu stránky a dalších. Pomocí Facebooku můžete:

* Sestavte svůj obchodní tok na základě dat, která získáte z Facebooku. 
* Při přijetí nového příspěvku použijte Trigger.
* Použijte akce, které se účtují na časovou osu, Získejte kanál stránky a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. Například když je na časové ose nový příspěvek, můžete tento příspěvek přenést a vložit ho do svého informačního kanálu Twitteru. 

Můžete začít vytvořením aplikace logiky hned teď v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Vytvoření připojení k Facebooku

Když přidáte tento konektor do vašich aplikací logiky, musíte autorizovat aplikace logiky pro připojení k Facebooku.

1. Přihlaste se k účtu Facebook.

2. Vyberte **autorizovat**a umožněte vašim aplikacím logiky připojení a používání Facebooku. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/facebook/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)