---
title: Vrátí znak Microsoft Translator Text API | Dokumentace Microsoftu
description: Jak Microsoft Translator Text API vrátí počet znaků.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "41987631"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Jak Microsoft Translator Text API vrátí počet znaků

Microsoft Translator se počítá každý znak vstupu. Znaky Unicode smysl, ne v bajtech. Náhrady kódu Unicode počet jako dva znaky. Prázdné znaky a značky se počítají jako znaky. Délka odpovědi, nezáleží.

Volání metody rozpoznat a BreakSentence započítávají spotřeby znak. Předpokládáme, že volání metody rozpoznat a BreakSentence jsou v rozumné poměru k použití jiné funkce, které jsou započteny. Společnost Microsoft si vyhrazuje právo spustit, počítací rozpoznat a BreakSentence. 

Další informace o počtu znaků je v [nejčastější dotazy k Microsoft Translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
