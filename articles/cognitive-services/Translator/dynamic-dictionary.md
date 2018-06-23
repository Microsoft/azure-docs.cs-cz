---
title: Překladač Microsoft Text API dynamický slovník | Microsoft Docs
description: Jak používat funkci dynamický slovník rozhraní API služby Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343787"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Jak používat funkci dynamický slovník rozhraní API služby Microsoft překladač textu

Pokud už znáte překladu, který chcete použít slovo nebo frázi, můžete je zadat jako značek v rámci žádosti. Je dynamický slovník je pouze bezpečné pro přístup z složená jako vlastní jména a názvy produktů. 

**Syntaxe:** 

< mstrans:dictionary překlad = "překlad frázi" > heslo < / mstrans:dictionary >

**Příklad: cs de:**

Zdroj vstup: slova < mstrans:dictionary překlad =\"wordomatic\"> slovo nebo frázi < / mstrans:dictionary > je položka slovníku.

Cíl výstupu: Das sladové "wordomatic" ist ein Wörterbucheintrag.

Tato funkce funguje stejně a bez režimu HTML. 

Funkce měli šetřit. Je vhodné a mnohem lepší způsob přizpůsobení překlad pomocí překladač centra společnosti Microsoft. Rozbočovače využívá úplnou kontextu a statistické pravděpodobnostech. Pokud máte nebo si může dovolit vytvořit Cvičná data, který ukazuje zaměstnavatelem nebo frázi v kontextu, získáte mnohem lepší výsledky. Můžete najít další informace o rozbočovači na [ http://hub.microsofttranslator.com ](http://hub.microsofttranslator.com).

