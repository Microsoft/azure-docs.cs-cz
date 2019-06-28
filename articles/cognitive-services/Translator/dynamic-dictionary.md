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
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436010"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak používat dynamický slovník

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů.

**Syntaxe:**

< mstrans:dictionary překlad = "překlad frázi" > frází < / mstrans:dictionary >

**Příklad: cs de:**

Vstupní zdroj: Slovo < mstrans:dictionary překlad =\"wordomatic\"> slova nebo fráze < / mstrans:dictionary > je ke slovníku.

Výstup cíle: DAS sladové "wordomatic" TIS ein Wörterbucheintrag.

Tato funkce funguje stejným způsobem a nemusíte režimu HTML.

Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o vlastní Translator na [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
