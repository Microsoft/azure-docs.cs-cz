---
title: Tok řízení Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Přečtěte si o toku ovládacích prvků Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f28b60d67e84e3e2e39cc647045a6dfca473b810
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932090"
---
# <a name="control-flow"></a>Tok řízení

Tento dokument popisuje tok řízení Conversation Learner (CL), jak je znázorněno v následujícím diagramu.

![](media/controlflow.PNG)

1. Uživatel zadá do robota termín nebo frázi, například "Co je počasí v Seattlu?"
1. CL předá vstup uživatele do modelu strojového učení, který extrahuje entity.
   - Tento model se sestavuje pomocí Conversation Learner a hostuje ho www.luis.ai.
1. Všechny extrahované entity a vstupní text uživatele jsou předány metodě zpětného volání detekce entit v kódu bot.
    - Tento kód může nastavit nebo vymazat hodnoty entit a manipulovat s nimi.
1. Síť CL neuronové pak vezme výstup extrakce entit a vstupu uživatele a vyhodnotí všechny akce definované v robotovi.
   - V tomto příkladu je výsledkem akce nejvyšší pravděpodobnost poskytnutí předpovědi počasí:

     ![](media/controlflow_forecast.PNG)

1. Vybraná akce v tomto případě vyžaduje volání rozhraní API pro načtení předpovědi počasí. 
1. Toto rozhraní API, které bylo zaregistrováno pomocí CL. Metoda AddCallback je pak vyvolána.  Výsledek tohoto rozhraní API se pak vrátí uživateli jako zpráva – například ' Slunečné s vysokou 67. '
1. Volání se pak provede v síti neuronové k určení další akce podle předchozího kroku.
1. Neuronové síť pak předpovídá další sadu možných akcí a vybraná akce se zobrazí uživateli, v tomto případě "cokoli jiného?"

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Postup výuky pomocí Conversation Learner](./how-to-teach-cl.md)
