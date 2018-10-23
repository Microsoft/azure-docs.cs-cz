---
title: Dynamický slovník – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak používat funkci dynamický slovník Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 0b96325b2d29abd230e4c389b176e97542a70282
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648228"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Jak používat funkci dynamický slovník Translator Text API

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů.

**Syntaxe:**

< mstrans:dictionary překlad = "překlad frázi" > frází < / mstrans:dictionary >

**Příklad: cs de:**

Zdroje vstupu: slovo < mstrans:dictionary překlad =\"wordomatic\"> slova nebo fráze < / mstrans:dictionary > je ke slovníku.

Cíl výstupu: Das sladové "wordomatic" TIS ein Wörterbucheintrag.

Tato funkce funguje stejným způsobem a nemusíte režimu HTML.

Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o vlastní Translator na [ http://aka.ms/CustomTranslator ](http://aka.ms/CustomTranslator).
