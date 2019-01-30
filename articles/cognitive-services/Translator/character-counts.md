---
title: Znak počty – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak rozhraní Translator Text API vrátí počet znaků.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226430"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak rozhraní Translator Text API vrátí počet znaků

Translator Text API se počítá každý znak vstupu. Znaky Unicode smysl, ne v bajtech. Náhrady kódu Unicode počet jako dva znaky. Prázdné znaky a značky se počítají jako znaky. Délka odpovědi, nezáleží.

Volání metody rozpoznat a BreakSentence započítávají spotřeby znak. Předpokládáme, že volání metody rozpoznat a BreakSentence jsou v rozumné poměru k použití jiné funkce, které jsou započteny. Společnost Microsoft si vyhrazuje právo spustit, počítací rozpoznat a BreakSentence.

Další informace o počtu znaků je v [nejčastější dotazy k Microsoft Translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
