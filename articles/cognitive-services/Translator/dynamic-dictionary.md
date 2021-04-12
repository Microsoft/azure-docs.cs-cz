---
title: Dynamický slovník – překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak používat funkci dynamického slovníku pro Azure Cognitive Services Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: ebfccb6fed46db65f6a70937516552f5280c8016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897760"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Použití dynamického slovníku

Pokud už znáte překlad, který chcete použít pro slovo nebo frázi, můžete ho v rámci žádosti dodat jako značku. Dynamický slovník je bezpečný pouze pro složená podstatná jména, jako jsou vhodné názvy a názvy produktů.

**Syntaktick**

<msTrans: slovníkové Translation = "překlady fráze" >fráze</msTrans: Dictionary>

**Požadavků**

* `From`Jazyky a `To` musí obsahovat angličtinu a jiný podporovaný jazyk. 
* `From`Místo použití funkce automatického rozpoznávání musíte uvést parametr v požadavku na překlad rozhraní API. 

**Příklad: en-de:**

Zdrojový vstup: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Cílový výstup: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Tato funkce funguje stejně jako v režimu HTML a bez něj.

Funkci používejte zřídka. Lepším způsobem přizpůsobení překladu je použití vlastního překladatele. Vlastní Překladatel umožňuje plné využití kontextových a statistických pravděpodobností. Pokud máte nebo můžete vytvořit školicí data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. Další informace o vlastní překladateli najdete na adrese [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .