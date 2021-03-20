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
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146607"
---
# <a name="content-moderator-review-tool"></a>Nástroj pro kontrolu Content Moderator

Azure Content Moderator poskytuje služby pro kombinování Moderování obsahu strojového učení s lidskými recenzemi. Web [nástrojů pro revize](https://contentmoderator.cognitive.microsoft.com) je uživatelsky přívětivý front-end, který poskytuje podrobný přístup k těmto službám.

## <a name="what-it-does"></a>Co dělá

[Nástroj pro revize](https://contentmoderator.cognitive.microsoft.com)při použití ve spojení s rozhraními API pro moderování s asistencí pro počítače umožňuje v procesu moderování obsahu provádět následující úlohy:

- Použijte jednu sadu nástrojů pro střední obsah ve více formátech (text, obrázek a video).
- Automatizujte vytváření lidských [recenzí](../review-api.md#reviews) , když do nich přijde výsledky rozhraní API pro moderování.
- Přiřaďte nebo přezkoumejte revize obsahu více kontrolám týmu, uspořádaných podle kategorie obsahu nebo úrovně zkušeností.
- Použijte výchozí nebo vlastní logické filtry ([pracovní postupy](../review-api.md#workflows)) k řazení a sledování obsahu bez psaní kódu.
- Pomocí [konektorů](./configure.md#connectors) můžete kromě rozhraní API Content moderator zpracovávat obsah pomocí služeb Microsoft PhotoDNA, analýza textu a obličeje.
- Získejte klíčové metriky výkonu pro procesy Moderování obsahu.

## <a name="review-tool-dashboard"></a>Kontrola řídicího panelu nástrojů

Na kartě **řídicí panel** vidíte klíčové metriky pro revize obsahu provedené v rámci nástroje. Podívejte se na celkový počet, kompletní a nedokončené recenze pro obrázek, text a video obsah. 

Tabulka **nedokončené recenze** zobrazuje rozpis uživatelů a dílčích týmů, které mají probíhající nebo dokončené recenze, a také zbývající čas smlouvy SLA. Můžete vybrat položky v tabulce a přejít na jejich recenze. Vyhledávací pole nad tabulkou umožňuje filtrovat výsledky podle názvu týmu a ikona **filtru** umožňuje filtrovat podle jiných metrik.

Přepnutí na kartu **dokončené recenze** zobrazuje celkový počet položek zpracovaných nebo dokončených uživateli a subskupinami. Tato data můžete filtrovat stejně jako nevyřízené recenze.

Kliknutím na text v pravém horním rohu řídicího panelu se zobrazí denní osobní metriky, které hlásí počet revizí dokončených pro každý typ obsahu.

> [!div class="mx-imgBorder"]
> ![Řídicí panel nástroje pro kontrolu v prohlížeči](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Kontrola přihlašovacích údajů nástroje

Při registraci pomocí [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com)budete vyzváni k výběru oblasti Azure pro účet. Důvodem je skutečnost, že [Nástroj pro kontrolu](https://contentmoderator.cognitive.microsoft.com) vygeneruje bezplatný zkušební klíč pro Azure Content moderator Services. Tento klíč budete potřebovat pro přístup ke všem službám ze volání REST nebo klientské sady SDK. Klíč a adresu URL koncového bodu rozhraní API si můžete zobrazit tak, že vyberete  >  **přihlašovací údaje** správce.

> [!div class="mx-imgBorder"]
> ![Content Moderator přihlašovací údaje](images/settings-6-credentials.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak získat přístup k nástrojům pro kontrolu a měnit nastavení, najdete v tématu [Konfigurace nástroje pro kontrolu](./configure.md) .