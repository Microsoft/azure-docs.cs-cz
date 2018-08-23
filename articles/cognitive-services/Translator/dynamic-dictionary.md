---
title: Microsoft Translator Text API dynamické slovníku | Dokumentace Microsoftu
description: Jak používat funkci dynamický slovník Microsoft Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: dbc754093827730b8709d67f314e5b327518ef50
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "41987650"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Jak používat funkci dynamický slovník Microsoft Translator Text API

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů. 

**Syntaxe:** 

< mstrans:dictionary překlad = "překlad frázi" > frází < / mstrans:dictionary >

**Příklad: cs de:**

Zdroje vstupu: slovo < mstrans:dictionary překlad =\"wordomatic\"> slova nebo fráze < / mstrans:dictionary > je ke slovníku.

Cíl výstupu: Das sladové "wordomatic" TIS ein Wörterbucheintrag.

Tato funkce funguje stejným způsobem a nemusíte režimu HTML. 

Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o vlastní Translator na [ http://aka.ms/CustomTranslator ](http://aka.ms/CustomTranslator).

