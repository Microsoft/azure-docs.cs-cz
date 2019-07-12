---
title: Dynamický slovník – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak používat funkci dynamický slovník Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: a815434cb8797acf6b92a8fe4a4f1ff69508975d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839217"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak používat dynamický slovník

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů.

**Syntaxe:**

< mstrans:dictionary překlad = "překlad frázi" > frází < / mstrans:dictionary >

**Požadavky:**

* `From` a `To` jazyky se musí lišit. 
* Je nutné uvést `From` parametru v žádosti o překlad API namísto použití auto-detect funkce. 

**Příklad: cs de:**

Vstupní zdroj: Slovo < mstrans:dictionary překlad =\"wordomatic\"> slova nebo fráze < / mstrans:dictionary > je ke slovníku.

Výstup cíle: DAS sladové "wordomatic" TIS ein Wörterbucheintrag.

Tato funkce funguje stejným způsobem a nemusíte režimu HTML.

Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o vlastní Translator na [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
