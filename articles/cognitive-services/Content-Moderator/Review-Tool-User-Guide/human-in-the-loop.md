---
title: Další koncepty nástroj pro revize - Content Moderator
titlesuffix: Azure Cognitive Services
description: Další informace o Content Moderator Zkontrolujte nástroj, web, který koordinuje kombinované AI a recenze prováděné lidmi moderování úsilí.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755891"
---
# <a name="content-moderator-review-tool"></a>Nástroj pro Content Moderator revize

Azure Content Moderator poskytuje služby machine learning pro moderování obsahu s recenze prováděné lidmi, zkombinovat a [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com) webu je uživatelsky přívětivé front-endu, která poskytuje podrobné přístup k těmto službám.

![Kontrola řídicího panelu nástroje v prohlížeči](./images/0-dashboard.png)

## <a name="what-it-does"></a>Co dělá

[Nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com), při použití ve spojení s moderování s podporou počítače rozhraní API, umožňuje provádění následujících úloh v procesu moderování obsahu:

- Jedna sada nástrojů můžete Moderovat obsah v různých formátech (text, image a videa).
- Automatizace vytváření lidských [kontroly](../review-api.md#reviews) při výsledků rozhraní API pro moderování přijde v.
- Přiřazení nebo eskalovat obsahu kontroly, aby mohla více týmů revize, uspořádané podle kategorie nebo role experience systému úroveň obsahu.
- Použít výchozí nebo vlastní logiku filtry ([pracovních postupů](../review-api.md#workflows)) řazení a sledovat obsah, aniž byste museli psát jakýkoli kód.
- Použití [konektory](./configure.md#connectors) se zpracování obsahu s Microsoft PhotoDNA, analýzu textu a rozhraní API pro rozpoznávání tváře kromě rozhraní API Content Moderatoru.
- Vytvořit vlastní konektor k vytváření pracovních postupů pro jakékoli rozhraní API nebo obchodních procesů.
- Získejte klíčové metriky výkonu na vašich procesů moderování obsahu.

## <a name="review-tool-dashboard"></a>Nástroj pro revize řídicí panel

Na **řídicí panel** kartu, můžete zobrazit klíčové metriky u obsahu revizí provést v rámci nástroje. Zobrazit číslo z celkového množství dokončené a probíhající kontroly pro obrázek, text a videa. Zobrazí se také rozdělení uživatelů a týmů, které dokončily revize, jakož i moderování značky, které byly použity.

![Zobrazit řídicí panel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Zkontrolujte přihlašovací údaje nástroje

Při registraci se [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com), budete vyzváni k výběru oblasti Azure pro účet. Důvodem je, že [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com) vygeneruje klíč bezplatnou zkušební verzi pro služby Azure Content Moderator; potřebujete tento klíč pro přístup ke kterékoli ze služeb z volání REST nebo Klientská sada SDK. Adresa URL vašeho rozhraní API a klíč koncového bodu můžete zobrazit tak, že vyberete **nastavení** > **pověření**.

![Content Moderator pověření](images/settings-6-credentials.png)

## <a name="next-steps"></a>Další postup

Zobrazit [nakonfigurovat nástroj pro recenze](./configure.md) Další informace o přístup k prostředkům nástroj pro kontrolu a změnit nastavení.