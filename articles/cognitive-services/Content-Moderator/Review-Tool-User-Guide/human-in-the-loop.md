---
title: Informace o konceptech nástrojů pro kontrolu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Přečtěte si o nástroji pro kontrolu Content Moderator, webu, který koordinuje kombinované navýšení souborů AI a recenze pro lidské revize.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169504"
---
# <a name="content-moderator-review-tool"></a>Nástroj pro kontrolu Content Moderator

Azure Content Moderator poskytuje služby pro kombinování Moderování obsahu strojového učení s lidskými recenzemi a web [nástrojů pro revize](https://contentmoderator.cognitive.microsoft.com) je uživatelsky přívětivý front-end, který poskytuje podrobný přístup k těmto službám.

![Řídicí panel nástroje pro kontrolu v prohlížeči](./images/0-dashboard.png)

## <a name="what-it-does"></a>Co dělá

[Nástroj pro revize](https://contentmoderator.cognitive.microsoft.com)při použití ve spojení s rozhraními API pro moderování s asistencí pro počítače umožňuje v procesu moderování obsahu provádět následující úlohy:

- Použijte jednu sadu nástrojů pro střední obsah ve více formátech (text, obrázek a video).
- Automatizujte vytváření lidských [recenzí](../review-api.md#reviews) , když do nich přijde výsledky rozhraní API pro moderování.
- Přiřaďte nebo přezkoumejte revize obsahu více kontrolám týmu, uspořádaných podle kategorie obsahu nebo úrovně zkušeností.
- Použijte výchozí nebo vlastní logické filtry ([pracovní postupy](../review-api.md#workflows)) k řazení a sledování obsahu bez psaní kódu.
- Pomocí [konektorů](./configure.md#connectors) můžete kromě rozhraní API Content moderator zpracovávat obsah pomocí služeb Microsoft PhotoDNA, analýza textu a obličeje.
- Sestavte si vlastní konektor pro vytváření pracovních postupů pro jakékoli rozhraní API nebo obchodní proces.
- Získejte klíčové metriky výkonu pro procesy Moderování obsahu.

## <a name="review-tool-dashboard"></a>Kontrola řídicího panelu nástrojů

Na kartě **řídicí panel** vidíte klíčové metriky pro revize obsahu provedené v rámci nástroje. Podívejte se na celkový počet, kompletní a nedokončené recenze pro obrázek, text a video obsah. Můžete také zobrazit rozpis uživatelů a týmů, kteří mají dokončené recenze, a také použité značky moderování.

![Zobrazit řídicí panel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Kontrola přihlašovacích údajů nástroje

Při registraci pomocí [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com)budete vyzváni k výběru oblasti Azure pro účet. Důvodem je skutečnost, že [Nástroj pro kontrolu](https://contentmoderator.cognitive.microsoft.com) vygeneruje bezplatný zkušební klíč pro Azure Content moderator Services; Tento klíč budete potřebovat pro přístup ke všem službám ze volání REST nebo klientské sady SDK. Klíč a adresu URL koncového bodu rozhraní API si můžete zobrazit tak, že vyberete **Nastavení**  >  **přihlašovací údaje**.

![Content Moderator přihlašovací údaje](images/settings-6-credentials.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak získat přístup k nástrojům pro kontrolu a měnit nastavení, najdete v tématu [Konfigurace nástroje pro kontrolu](./configure.md) .