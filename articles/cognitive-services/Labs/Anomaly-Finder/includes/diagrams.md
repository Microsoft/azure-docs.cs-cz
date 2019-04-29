---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709845"
---
Vrácená data s očekávanou hodnotou a výchozím horním a spodním okrajem. V praxi můžete definovat parametr [sensitivity] (citlivost) a potom použít (ExpectedValue + citlivost * UpperMargin) jako horní mez a (ExpectedValue – citlivost * LowerMargin) jako spodní mez a vyladit bod anomálie sami. Hodnota [sensitivity] by měla být větší než 1. Níže najdete některé diagramy pro ladění.

> [!NOTE]
> Ukázková aplikace diagramy negeneruje. Vytvářejí se pomocí samostatného nástroje ve spolupráci s ukázkovou aplikací.

![Ladění: citlivost = 1,0](../media/sensitivity_1.png)
![Ladění: citlivost = 1,5](../media/sensitivity_1.5.png)
![Ladění: citlivost = 2](../media/sensitivity_2.png)
![Ladění: citlivost = 3,5](../media/sensitivity_3.5.png)
