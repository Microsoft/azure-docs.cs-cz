---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "75379283"
---
Prověřte `"confidence"` hodnoty pro každý výsledek klíč/hodnota pod `"pageResults"` uzlem. Měli byste se také podívat na skóre spolehlivosti v `"readResults"` uzlu, který odpovídá operaci čtení textu. Spolehlivost výsledků čtení neovlivňuje spolehlivost výsledků extrakce klíč/hodnota, proto byste měli zaškrtnout obě.
* Pokud jsou skóre spolehlivosti pro operaci čtení nízká, zkuste zlepšit kvalitu vstupních dokumentů (viz [požadavky na vstup](../overview.md#input-requirements)).
* Pokud jsou skóre spolehlivosti pro operaci extrakce klíč/hodnota nízká, zajistěte, aby analyzované dokumenty byly stejného typu jako dokumenty používané v sadě školení. Pokud se v dokumentech v sadě školení nacházejí změny, zvažte jejich rozdělení do různých složek a školení jednoho modelu pro každou variaci.

Hodnocení spolehlivosti bude záviset na vašem případu použití, ale obecně je dobrým zvykem zaměřit se na skóre 80% nebo vyšší. Pro citlivější případy, jako je čtení lékařských záznamů nebo výpisů faktur, se doporučuje skóre 100%.