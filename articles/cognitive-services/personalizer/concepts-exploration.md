---
title: Průzkum - Personalizátor
titleSuffix: Azure Cognitive Services
description: S průzkumem, Personalizace je schopen i nadále poskytovat dobré výsledky, i když změny chování uživatelů. Výběr nastavení průzkumu je obchodní rozhodnutí o podílu interakcí uživatelů, které je třeba prozkoumat, s cílem zlepšit model.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490811"
---
# <a name="exploration-and-exploitation"></a>Průzkum a těžba

S průzkumem, Personalizace je schopen i nadále poskytovat dobré výsledky, i když změny chování uživatelů.

Když personalizátor obdrží volání rank, vrátí RewardActionID, které buď:
* Používá využití tak, aby odpovídalo nejpravděpodobnějšímu chování uživatele na základě aktuálního modelu strojového učení.
* Používá průzkum, který neodpovídá akci, která má nejvyšší pravděpodobnost v pořadí.

Personalizátář v současné době používá algoritmus s názvem *epsilon chamtivý* prozkoumat. 

## <a name="choosing-an-exploration-setting"></a>Výběr nastavení průzkumu

Procento provozu, které se má použít pro průzkum, nakonfigurujete na stránce **Konfigurace** portálu Azure pro personalizátor. Toto nastavení určuje procento volání pořadí, které provádějí průzkum. 

Personalizátor určuje, zda se má s touto pravděpodobností prozkoumat nebo zneužít při každém volání hodnosti. To se liší od chování v některých A /B rozhraní, které uzamknout ošetření na konkrétní ID uživatele.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Doporučené postupy pro výběr nastavení průzkumu

Výběr nastavení průzkumu je obchodní rozhodnutí o podílu interakcí uživatelů, které je třeba prozkoumat, s cílem zlepšit model. 

Nastavení nula negovat mnoho výhod Personalizace. Pomocí tohoto nastavení personalizace nepoužívá žádné interakce uživatele k objevování lepších interakcí uživatelů. To vede ke stagnaci modelu, drift, a nakonec nižší výkon.

Nastavení, které je příliš vysoké, bude negovat výhody učení se z chování uživatelů. Nastavení na 100 % znamená konstantní randomizaci a žádné naučené chování uživatelů by výsledek neovlivnilo.

Je důležité neměnit chování aplikace na základě toho, zda zjistíte, zda personalizáta zkoumá nebo využívá. To by vedlo k učení předsudky, které by v konečném důsledku snížit potenciální výkon.

## <a name="next-steps"></a>Další kroky

[Zpětnovazební učení](concepts-reinforcement-learning.md) 