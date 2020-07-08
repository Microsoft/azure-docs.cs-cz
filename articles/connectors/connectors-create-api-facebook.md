---
title: Připojení k Facebooku
description: Automatizace úloh a pracovních postupů, které spravují časovou osu a stránku Facebooku pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665798"
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