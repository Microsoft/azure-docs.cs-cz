---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734022"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Výstrahy metriky (klasické) |100 aktivní pravidla výstrah pro každé předplatné. | Podpora volání |
| Upozornění metrik |5 000 aktivní pravidla upozornění na předplatné v Azure Public, Azure Čína 21Vianet a Azure Government cloudy. Pokud tento limit zasáhnete, prozkoumejte, jestli můžete použít [stejný typ výstrah s více prostředky](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 metrika času – řada na pravidlo výstrahy. | Zavolejte podporu. |
| Upozornění protokolu aktivit | 100 aktivní pravidla výstrah pro každé předplatné (nelze zvýšit). | Stejné jako výchozí |
| Výstrahy protokolu | 512 aktivní pravidla výstrah pro každé předplatné. 200 aktivní pravidla upozornění na prostředek. | Podpora volání |
| Pravidla upozornění a délka popisu pravidel akcí| Výstrahy prohledávání protokolu 4096 znaků<br/>Všechny ostatní znaky 2048 | Stejné jako výchozí |