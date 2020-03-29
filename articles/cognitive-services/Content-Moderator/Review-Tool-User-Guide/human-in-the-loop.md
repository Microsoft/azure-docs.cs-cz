---
title: Naučte se koncepty nástrojů pro kontrolu – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o nástroji Content Moderator Review, webové stránce, která koordinuje kombinovanou umělou a umělou a lidskou kontrolu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169504"
---
# <a name="content-moderator-review-tool"></a>Nástroj revize moderátora obsahu

Azure Content Moderator poskytuje služby pro kombinaci moderování obsahu strojového učení s lidskými recenzemi a web [nástroje revize](https://contentmoderator.cognitive.microsoft.com) je uživatelsky přívětivý front-end, který poskytuje podrobný přístup k těmto službám.

![Řídicí panel nástroje pro kontrolu v prohlížeči](./images/0-dashboard.png)

## <a name="what-it-does"></a>Co dělá

[Nástroj revize](https://contentmoderator.cognitive.microsoft.com), pokud se používá ve spojení s uvolděnými pomocí nastavení API moderování, umožňuje provádět následující úkoly v procesu moderování obsahu:

- Pomocí jedné sady nástrojů můžete moderovat obsah ve více formátech (text, obrázek a video).
- Automatizujte vytváření lidských [recenzí,](../review-api.md#reviews) když přijdou výsledky rozhraní API pro moderování.
- Přiřaďte nebo eskalujte recenze obsahu více týmům pro recenze, které jsou uspořádány podle kategorie obsahu nebo úrovně prostředí.
- Pomocí výchozích nebo vlastních[filtrů logiky](../review-api.md#workflows)( pracovních postupů ) můžete seřadit a sledovat obsah bez psaní kódu.
- Kromě rozhraní API pro moderátora obsahu můžete pomocí [konektorů](./configure.md#connectors) zpracovávat obsah pomocí služeb Microsoft PhotoDNA, Text Analytics a Face.
- Vytvořte si vlastní konektor pro vytváření pracovních postupů pro libovolné rozhraní API nebo obchodní proces.
- Získejte klíčové metriky výkonu procesů moderování obsahu.

## <a name="review-tool-dashboard"></a>Řídicí panel nástroje revize

Na kartě **Řídicí panel** uvidíte klíčové metriky pro recenze obsahu provedené v nástroji. Podívejte se na celkový počet, úplných a nevyřízených recenzí pro obrázek, text a video obsah. Můžete také zobrazit rozdělení uživatelů a týmů, kteří dokončili recenze, a také značky moderování, které byly použity.

![Zobrazit řídicí panel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Zkontrolovat pověření nástroje

Když se zaregistrujete pomocí [nástroje revize](https://contentmoderator.cognitive.microsoft.com), budete vyzváni k výběru oblasti Azure pro vás účet. Důvodem je, [že nástroj revize](https://contentmoderator.cognitive.microsoft.com) generuje bezplatný zkušební klíč pro služby Azure Content Moderator; Tento klíč budete potřebovat pro přístup ke všem službám z volání REST nebo sady SDK klienta. Adresu URL koncového bodu klíče a rozhraní API můžete zobrazit tak, že vyberete **Nastavení** > **přihlašovacích údajů**.

![Pověření moderátora obsahu](images/settings-6-credentials.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak získat přístup k prostředkům nástroje Revize a změnit nastavení, [najdete v tématu Konfigurace nástroje Revize.](./configure.md)