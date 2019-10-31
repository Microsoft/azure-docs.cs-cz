---
title: Dynamický slovník – Translator Text API
titleSuffix: Azure Cognitive Services
description: Jak používat funkci dynamického slovníku Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c268616c015c1c8735b2bdddc057f235d02cdc2a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161738"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Použití dynamického slovníku

Pokud už znáte překlad, který chcete použít pro slovo nebo frázi, můžete ho v rámci žádosti dodat jako značku. Dynamický slovník je bezpečný jenom pro složená podstatná jména, jako jsou správné názvy a názvy produktů.

**Syntaktick**

< msTrans: slovníkové Translation = "překlady fráze" > fráze </msTrans: Dictionary >

**Požadavků**

* Jazyky `From` a `To` musí být odlišné. 
* Místo použití funkce automatického rozpoznávání musíte do žádosti o překlad rozhraní API zahrnout parametr `From`. 

**Příklad: en-de:**

Zdrojový vstup: Word < msTrans: Dictionary Translation =\"wordomatic\"> Word nebo fráze </msTrans: Dictionary > je položka slovníku.

Cílový výstup: Das Wort "wordomatic" tis Ein Wörterbucheintrag.

Tato funkce funguje stejně jako v režimu HTML a bez něj.

Funkce by se měla používat zřídka. Vhodný a mnohem lepší způsob přizpůsobení překladu je použití vlastního překladatele. Vlastní Překladatel umožňuje plné využití kontextových a statistických pravděpodobností. Pokud máte nebo můžete vytvořit školicí data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. Další informace o vlastních překladatelích najdete na [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
