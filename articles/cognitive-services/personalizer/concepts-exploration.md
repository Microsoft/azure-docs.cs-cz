---
title: Zkoumání - Personalizer
titleSuffix: Azure Cognitive Services
description: Zkoumání Personalizer je moci pokračovat v poskytování dobré výsledky, i když se změní chování uživatelů. Výběr prostředí zkoumání je obchodní rozhodnutí o podíl interakce uživatele s, za účelem zlepšení modelu.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: ebb59b6bb7c36f4558b2bd63d2d55fa95823c4c3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722480"
---
# <a name="exploration-and-exploitation"></a>Zkoumání a využití

Zkoumání Personalizer je moci pokračovat v poskytování dobré výsledky, i když se změní chování uživatelů.

Pořadí volání přijetí Personalizer vrátí RewardActionID, která buď:
* Využívání používá tak, aby odpovídaly nejpravděpodobnější chování uživatelů podle aktuální model strojového učení.
* Používá průzkumu, který neodpovídá akci, která má největší pravděpodobnost v pořadí.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizer aktuálně používá algoritmus volá *epsilon greedy* prozkoumat. 

## <a name="choosing-an-exploration-setting"></a>Výběr prostředí průzkum

Můžete nakonfigurovat procentuální hodnotu provoz určený pro zkoumání webu Azure Portal **nastavení** stránka Personalizer. Toto nastavení určuje procento pořadí volání, které provádějí průzkum. 

Personalizer Určuje, jestli se má prozkoumat nebo jejich zneužití s této pravděpodobnosti pro všechna pořadí volání. To se liší od chování v některých A / B architektur, které zámky ošetření na konkrétní ID uživatele.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Osvědčené postupy pro výběr prostředí průzkum

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Výběr prostředí zkoumání je obchodní rozhodnutí o podíl interakce uživatele s, za účelem zlepšení modelu. 

Nastavení nula bude negovat mnohé z výhod Personalizer. S tímto nastavením Personalizer použije bez interakce uživatelů ke zjištění lepší interakci uživatele. To vede k modelu stagnation odchylek a nakonec nižší výkon.

Nastavení, které je příliš vysoká. bude negovat výhody učení z chování uživatelů. Ji nastavíte na 100 % znamená konstantní náhodné a jakékoli zjištěná chování uživatelů by ovlivňovali výsledek.

Je důležité, abyste změnit chování aplikací podle toho, jestli zjistíte, pokud je Personalizer zkoumání nebo zneužití. To vedlo k učení chyb, které by nakonec snížit potenciální výkon.

## <a name="next-steps"></a>Další postup

[Zpětnovazebnému učení](concepts-reinforcement-learning.md) 