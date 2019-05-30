---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271494"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Nastavení automatického škálování |100 na oblast a předplatné. | Stejné jako výchozí. |
| Upozornění na metriky (klasické) |100 aktivních pravidel upozornění na jedno předplatné. | Obraťte se na podporu. |
| Upozornění metrik |1 000 aktivních pravidel upozornění pro každé předplatné (veřejným cloudům) a 100 aktivní pravidla upozornění na jedno předplatné Azure China a Azure Government. | Obraťte se na podporu. |
| Upozornění protokolu aktivit | 100 aktivních pravidel upozornění na jedno předplatné. | Stejné jako výchozí. |
| Výstrahy protokolu | 512 | Obraťte se na podporu. |
| Skupiny akcí |2 000 skupin akcí na jedno předplatné. | Obraťte se na podporu. |

**Omezení konkrétní skupiny akcí**

| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Nabídky Azure aplikace | 10 akcí azure aplikace na skupinu akcí. | Obraťte se na podporu. |
| Email | 1 000 e-mailových akcí do skupiny akcí. Viz také [tady informace](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Obraťte se na podporu. |
| ITSM | 10 akce ITSM ve skupině akcí. | Obraťte se na podporu. | 
| Aplikace logiky | do skupiny akcí 10 akce logic Apps. | Obraťte se na podporu. |
| Runbook | 10 akce sady runbook do skupiny akcí. | Obraťte se na podporu. |
| SMS | 10 SMS akce v skupiny akcí. Viz také [tady informace](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Obraťte se na podporu. |
| Hlas | 10 hlasové akce v skupiny akcí. Viz také [tady informace](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Obraťte se na podporu. |
| Webhook | 10 akce webhooku v skupiny akcí.  Maximální počet volání webhooku je 1500 za minutu na jedno předplatné. Další omezení najdete na adrese [informace specifické pro akce](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Obraťte se na podporu. |
