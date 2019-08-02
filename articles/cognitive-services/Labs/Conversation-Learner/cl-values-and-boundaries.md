---
title: Conversation Learner výchozí konfigurace – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Přečtěte si o výchozí konfiguraci Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705293"
---
# <a name="default-values-and-boundaries"></a>Výchozí hodnoty a hranice

Tento dokument popisuje výchozí konfiguraci Conversation Learner a hranice klíčových služeb.

## <a name="default-configuration"></a>Výchozí konfigurace

Parametr | Výchozí hodnota
--- | --- 
Výchozí časový limit relace | 30 minut

## <a name="boundaries"></a>Hranice

Parametr | Omezení
--- | --- 
Vytváření rozhraní API, maximální počet volání HTTP za měsíc | 5 MIN
Vytváření rozhraní API, maximální počet volání HTTP za sekundu | 25
Rozhraní API relace, maximální počet volání HTTP za měsíc | 500 000
Rozhraní API relace, maximální počet volání HTTP za sekundu | 10
Maximální počet vlastních (neprogramových) entit na model | Zobrazit [Luis hranice dokumentu](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); v praxi může být skutečný počet mírně menší
Maximální počet předem sestavených entit na model | Zobrazit [Luis hranice dokumentu](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Maximální počet entit (celkem) na model | 100
Maximální počet akcí na model | 32
Maximální počet dialogových oken vlaků na model | 1000
Maximální počet uživatelů, které se aktivují na jeden vlakový dialog | 100
Maximální počet dialogových oken protokolu na model | Bez předem nastaveného limitu, ale dialogy protokolu se uchovávají jenom pro pevné období, než se zahodí.  Uživatelské rozhraní Conversation Learner také bude zobrazovat dialogová okna protokolu 100. 
Maximální počet modelů na uživatele | Bez předem nastaveného limitu
Maximální počet sekvenčních akcí, které nečekají na čekání | 5 (*)

(*) Po 5 sekvenčních nečekacích akcích jsou všechny nečekací akce maskovány a Conversation Learner se budou vybírat mezi dostupnými akcemi čekání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s Conversation Learner](./quickstart.md)
