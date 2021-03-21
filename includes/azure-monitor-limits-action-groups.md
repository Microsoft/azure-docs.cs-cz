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
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734020"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Nabízená oznámení aplikací Azure | 10 akcí aplikace Azure na skupinu akcí. | Stejné jako výchozí |
| E-mail | 1 000 akcí e-mailu ve skupině akcí<br>Za hodinu nepřekračuje 100 e-mailů.<br>Přečtěte si také [informace o omezení rychlosti](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Stejné jako výchozí |
| ITSM | 10 akcí ITSM ve skupině akcí. | Stejné jako výchozí | 
| Aplikace logiky | 10 akcí aplikace logiky ve skupině akcí. | Stejné jako výchozí |
| Runbook | 10 akcí sady Runbook ve skupině akcí. | Stejné jako výchozí |
| SMS | 10 akcí SMS ve skupině akcí.<br>Každých 5 minut se nejedná o více než 1 zprávu SMS.<br>Přečtěte si také [informace o omezení rychlosti](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Stejné jako výchozí |
| Hlas | 10 akcí hlasu ve skupině akcí.<br>Nejedná se o více než jedno volání hlasu každých 5 minut.<br>Přečtěte si také [informace o omezení rychlosti](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Stejné jako výchozí |
| Webhook | 10 akcí Webhooku ve skupině akcí.  Maximální počet volání webhooků je 1500 za minutu na jedno předplatné. Další omezení jsou k dispozici v [informacích specifických pro akci](../articles/azure-monitor/alerts/action-groups.md#action-specific-information).  | Stejné jako výchozí |