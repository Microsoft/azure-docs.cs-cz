---
title: Dynamický slovník – Translator Text API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak používat funkci dynamického slovníku Translator Text API Azure Cognitive Services.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75446715"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Použití dynamického slovníku

Pokud už znáte překlad, který chcete použít pro slovo nebo frázi, můžete ho v rámci žádosti dodat jako značku. Dynamický slovník je bezpečný pouze pro složená podstatná jména, jako jsou vhodné názvy a názvy produktů.

**Syntaxe:**

<msTrans: slovníkové Translation = "překlady fráze" >fráze</msTrans: Dictionary>

**Požadavků**

* Jazyky `From` a `To` musí obsahovat angličtinu a jiný podporovaný jazyk. 
* Místo použití funkce automatického `From` rozpoznávání musíte uvést parametr v požadavku na překlad rozhraní API. 

**Příklad: en-de:**

Zdrojový vstup:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Cílový výstup:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Tato funkce funguje stejně jako v režimu HTML a bez něj.

Funkci používejte zřídka. Lepším způsobem přizpůsobení překladu je použití vlastního překladatele. Vlastní Překladatel umožňuje plné využití kontextových a statistických pravděpodobností. Pokud máte nebo můžete vytvořit školicí data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. Další informace o vlastní překladateli najdete na adrese [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
