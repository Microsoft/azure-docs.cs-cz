---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 506270b1828e98f14e3fe7a84b7f780e209e2669
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165582"
---
Vrácená data s očekávanou hodnotou a výchozím horním a spodním okrajem. V praxi můžete definovat parametr [sensitivity] (citlivost) a potom použít (ExpectedValue + citlivost * UpperMargin) jako horní mez a (ExpectedValue – citlivost * LowerMargin) jako spodní mez a vyladit bod anomálie sami. Hodnota [sensitivity] by měla být větší než 1. Níže najdete některé diagramy pro ladění.

> [!NOTE]
> Ukázková aplikace diagramy negeneruje. Vytvářejí se pomocí samostatného nástroje ve spolupráci s ukázkovou aplikací.

![Ladění: citlivost = 1,0](../media/sensitivity_1.png)
![Ladění: citlivost = 1,5](../media/sensitivity_1.5.png)
![Ladění: citlivost = 2](../media/sensitivity_2.png)
![Ladění: citlivost = 3,5](../media/sensitivity_3.5.png)