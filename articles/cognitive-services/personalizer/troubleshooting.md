---
title: Řešení potíží – přizpůsobení
titleSuffix: Azure Cognitive Services
description: V tomto článku najdete otázky týkající se řešení potíží s přizpůsobením.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955224"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s přizpůsobením

Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.

## <a name="learning-loop"></a>Výuková smyčka

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Výuková smyčka se zřejmě neučí. Jak to můžu vyřešit?

Výuková smyčka potřebuje několik tisíc volání odměna před tím, než volání pořadí zařadí přednost. 

Pokud si nejste jistí, jak se vaše výuková smyčka právě chová, spusťte [testování offline](concepts-offline-evaluation.md)a použijte opravené zásady učení. 

## <a name="next-steps"></a>Další postup

[Konfigurace frekvence aktualizace modelu](how-to-settings.md#model-update-frequency)