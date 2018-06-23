---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343004"
---
S daty vrácenými s očekávanou hodnotou a výchozí horní a dolní okraj. V praxi, můžete definovat [citlivosti] parametr a pak použít (ExpectedValue + velkých a malých písmen * UpperMargin) jako horní mez a (ExpectedValue - citlivosti * LowerMargin) jako dolní hranice pro optimalizaci anomálií bodů podle sami sobě. Hodnota [citlivosti] musí být větší než 1. Níže jsou uvedeny některé diagramy pro ladění.

> [!NOTE]
> Diagramy nejsou generované ukázkovou aplikaci. Jsou vytvářeny jako samostatný nástroj s ukázkovou aplikací.

![Tunning: citlivosti = 1.0](../media/sensitivity_1.png)
![Tunning: velkých a malých písmen = 1.5](../media/sensitivity_1.5.png)
![Tunning: velkých a malých písmen = 2](../media/sensitivity_2.png)
![Tunning: velkých a malých písmen = 3.5](../media/sensitivity_3.5.png)