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
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595290"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Použití dynamického slovníku

Pokud už znáte překlad, který chcete použít pro slovo nebo frázi, můžete ho v rámci žádosti dodat jako značku. Dynamický slovník je bezpečný jenom pro složená podstatná jména, jako jsou správné názvy a názvy produktů.

**Syntaktick**

< msTrans: slovníkové Translation = "překlady fráze" > fráze </msTrans: Dictionary >

**Požadavků**

* Jazyky `From` a`To` se musí lišit. 
* Místo použití funkce automatického `From` rozpoznávání musíte uvést parametr v požadavku na překlad rozhraní API. 

**Příklad: en-de:**

Zdrojový vstup: Slovo < msTrans: Dictionary Translation =\"wordomatic\"> Word nebo fráze </msTrans: Dictionary > je položka slovníku.

Cílový výstup: Das Wort "wordomatic" tis Ein Wörterbucheintrag.

Tato funkce funguje stejně jako v režimu HTML a bez něj.

Funkce by se měla používat zřídka. Vhodný a mnohem lepší způsob přizpůsobení překladu je použití vlastního překladatele. Vlastní Překladatel umožňuje plné využití kontextových a statistických pravděpodobností. Pokud máte nebo můžete vytvořit školicí data, která zobrazují vaši práci nebo frázi v kontextu, získáte mnohem lepší výsledky. Další informace o vlastní překladateli najdete na adrese [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
