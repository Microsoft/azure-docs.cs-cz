---
title: Řešení potíží – přizpůsobení
titleSuffix: Azure Cognitive Services
description: V tomto článku najdete otázky týkající se řešení potíží s přizpůsobením.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663687"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s přizpůsobením

Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.

## <a name="learning-loop"></a>Výuková smyčka

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Výuková smyčka se zřejmě neučí. Jak to můžu vyřešit?

Výuková smyčka potřebuje několik tisíc volání odměna před tím, než volání pořadí zařadí přednost. 

Pokud si nejste jistí, jak se vaše výuková smyčka právě chová, spusťte [testování offline](concepts-offline-evaluation.md)a použijte opravené zásady učení. 

## <a name="next-steps"></a>Další postup

[Konfigurace frekvence aktualizace modelu](how-to-settings.md#model-update-frequency)