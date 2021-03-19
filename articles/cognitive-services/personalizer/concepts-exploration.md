---
title: Vlastní nastavení pro průzkum
titleSuffix: Azure Cognitive Services
description: V případě průzkumu může přizpůsobený uživatel pokračovat v poskytování dobrých výsledků, i když se změní chování uživatele. Výběr nastavení průzkumu je obchodní rozhodnutí o poměru interakcí uživatelů, které se mají prozkoumat, aby bylo možné model vylepšit.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87128144"
---
# <a name="exploration-and-exploitation"></a>Zkoumání a využití

V případě průzkumu může přizpůsobený uživatel pokračovat v poskytování dobrých výsledků, i když se změní chování uživatele.

Když přizpůsobené přizpůsobuje volání pořadí, vrátí RewardActionID, který buď:
* Používá využívání, které odpovídá nejpravděpodobnějšímu chování uživatelů na základě aktuálního modelu strojového učení.
* Používá průzkum, který se neshoduje s akcí, která má nejvyšší pravděpodobnost v pořadí.

Přizpůsobené přizpůsobování v současnosti používá algoritmus s názvem *Epsilon hlad* . 

## <a name="choosing-an-exploration-setting"></a>Výběr nastavení průzkumu

Procentuální hodnotu provozu, který se má použít pro průzkum, nakonfigurujete na stránce **konfigurace** Azure Portal pro přizpůsobení. Toto nastavení určuje procento volání pořadí, která provádějí průzkum. 

Přizpůsobení určuje, zda se má tato pravděpodobnost prozkoumat nebo zneužít při každém volání pořadí. To se liší od chování v některých architekturách A/B, které zablokují zpracování pro konkrétní ID uživatelů.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Osvědčené postupy pro výběr nastavení průzkumu

Výběr nastavení průzkumu je obchodní rozhodnutí o poměru interakcí uživatelů, které se mají prozkoumat, aby bylo možné model vylepšit. 

Nastavení nuly bude mít na starosti mnoho výhod přizpůsobeného aplikace. Pomocí tohoto nastavení přizpůsobené přizpůsobování nevyužívá žádné uživatelské interakce, aby bylo možné zjistit lepší uživatelské interakce. To vede k modelování stagnation, snížení rychlosti a konečnému snížení výkonu.

Nastavení, které je příliš vysoké, bude mít na starosti výhody učení od chování uživatele. Nastavení na 100% implikuje konstantní náhodnost a jakékoli zjištěné chování uživatelů by neovlivnilo výsledek.

Je důležité, abyste neměnili chování aplikace na základě toho, jestli se vám zobrazuje, jestli se dá přizpůsobovat nebo zneužít. To by vedlo k případným posunům, které by nakonec snížily potenciální výkon.

## <a name="next-steps"></a>Další kroky

[Zpětnovazební učení](concepts-reinforcement-learning.md) 