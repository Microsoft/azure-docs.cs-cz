---
title: Připojení k Facebooku
description: Automatizace úloh a pracovních postupů, které spravují vaši časovou osu a stránku na Facebooku pomocí Aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665798"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Správa časové osy a stránky na Facebooku pomocí Aplikací Azure Logic Apps

Připojte se k Facebooku a zveřejňujte příspěvky na timeline, získejte informační kanál stránky a další informace. S Facebookem můžete:

* Sestavte si svůj obchodní tok na základě dat, která získáte z Facebooku. 
* Při přijetí nového příspěvku použijte aktivační událost.
* Použijte akce, které zveřejníte na vašem profilu Timeline, získejte informační kanál stránky a další. Tyto akce získat odpověď a potom zpřístupnit výstup pro jiné akce. Pokud je například na vašem profilu Timeline nový příspěvek, můžete tento příspěvek vzít a odeslat jej do kanálu Twitteru. 

Můžete začít vytvořením aplikace logiky teď, najdete v [tématu Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Vytvoření připojení k Facebooku

Když přidáte tento konektor do aplikací logiky, musíte autorizovat aplikace logiky pro připojení k Facebooku.

1. Přihlaste se ke svému účtu na Facebooku.

2. Vyberte **Autorizovat**a povolte aplikacím logiky připojení a používání Facebooku. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru OpenAPI (dříve Swagger) konektoru, naleznete [na referenční stránce konektoru](/connectors/facebook/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)