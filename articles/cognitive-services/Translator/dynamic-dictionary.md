---
title: Dynamický slovník – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak používat funkci dynamický slovník Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: e524df191bc7e621d5b048a373a8c424fbe2a721
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214139"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Jak používat funkci dynamický slovník Translator Text API

Pokud již znáte překlad, kterou chcete použít pro slovo nebo frázi, můžete je zadat jako kód v rámci požadavku. Je dynamický slovník je jen bezpečný pro složená, jako jsou vlastní názvy a názvy produktů.

**Syntaxe:**

< mstrans:dictionary překlad = "překlad frázi" > frází < / mstrans:dictionary >

**Příklad: cs de:**

Vstupní zdroj: Slovo < mstrans:dictionary překlad =\"wordomatic\"> slova nebo fráze < / mstrans:dictionary > je ke slovníku.

Výstup cíle: DAS sladové "wordomatic" TIS ein Wörterbucheintrag.

Tato funkce funguje stejným způsobem a nemusíte režimu HTML.

Tato funkce by měly používat střídmě. Odpovídající a mnohem lepší způsob přizpůsobení překladu je s použitím překladač vlastní. Vlastní Translator umožňuje plně využívat kontextu a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazuje vaše firma nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o vlastní Translator na [ http://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
