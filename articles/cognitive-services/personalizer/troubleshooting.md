---
title: Řešení potíží – přizpůsobení
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953164"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s přizpůsobením

Tento článek obsahuje odpovědi na nejčastější dotazy k řešení potíží týkajících se přizpůsobení.

## <a name="learning-loop"></a>Výuková smyčka

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Výuková smyčka se zřejmě neučí. Návody opravit?

Výuková smyčka potřebuje několik tisíc volání odměna před tím, než volání pořadí zařadí přednost. 

Pokud si nejste jistí, jak se vaše výuková smyčka právě chová, spusťte [testování offline](concepts-offline-evaluation.md)a použijte opravené zásady učení. 

## <a name="next-steps"></a>Další kroky

[Konfigurace frekvence aktualizace modelu](how-to-settings.md#model-update-frequency)