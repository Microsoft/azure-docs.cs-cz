---
title: Dynamický slovník - Překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak používat funkci dynamického slovníku rozhraní Api pro překladač překladač Azure Cognitive Services Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446715"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Jak používat dynamický slovník

Pokud již znáte překlad, který chcete použít u slova nebo fráze, můžete jej zadat jako značku v žádosti. Dynamický slovník je bezpečný pouze pro složená vlastní jména, jako jsou vlastní názvy a názvy produktů.

**Syntaxe:**

<mstrans:slovníkový překlad="překlad fráze">fráze</mstrans:slovník>

**Požadavky:**

* `From` Jazyky `To` a musí obsahovat angličtinu a další podporovaný jazyk. 
* Parametr je `From` nutné zahrnout do požadavku na překlad rozhraní API namísto použití funkce automatického rozpoznání. 

**Příklad: en-de:**

Zdrojový vstup:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Cílový výstup:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Tato funkce funguje stejným způsobem s režimem HTML i bez něj.

Používejte funkci střídmě. Lepší způsob, jak přizpůsobit překlad pomocí vlastního překladače. Vlastní Překladač plně využívá kontextové a statistické pravděpodobnosti. Pokud máte nebo můžete vytvořit trénovací data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. Více informací o vlastním překladači naleznete na adrese [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
