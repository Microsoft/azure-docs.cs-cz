---
title: Znak počty – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak rozhraní Translator Text API vrátí počet znaků.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649190"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak rozhraní Translator Text API vrátí počet znaků

Translator Text API se počítá každý znak vstupu. Znaky Unicode smysl, ne v bajtech. Náhrady kódu Unicode počet jako dva znaky. Prázdné znaky a značky se počítají jako znaky. Délka odpovědi, nezáleží.

Volání metody rozpoznat a BreakSentence započítávají spotřeby znak. Předpokládáme, že volání metody rozpoznat a BreakSentence jsou v rozumné poměru k použití jiné funkce, které jsou započteny. Společnost Microsoft si vyhrazuje právo spustit, počítací rozpoznat a BreakSentence.

Další informace o počtu znaků je v [nejčastější dotazy k Microsoft Translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
