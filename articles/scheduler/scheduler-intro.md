---
title: Co je Azure Scheduler? | Dokumentace Microsoftu
description: Azure Scheduler vám umožní deklarativně popisovat akce, které mají běžet v cloudu. Potom naplánuje a automaticky spustí tyto akce.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22715110"
---
# <a name="what-is-azure-scheduler"></a>Co je Azure Scheduler?
Azure Scheduler vám umožní deklarativně popisovat akce, které mají běžet v cloudu. Potom naplánuje a automaticky spustí tyto akce.  Scheduler k tomu použije [portál Azure](scheduler-get-started-portal.md), kód, [REST API](https://msdn.microsoft.com/library/mt629143.aspx) nebo Azure PowerShell.

Scheduler vytváří, udržuje a vyvolává naplánovanou práci.  Scheduler nehostuje žádné úlohy ani nespouští žádný kód. On *vyvolává* kód hostovaný jinde – v Azure, v lokálním umístění nebo u jiného poskytovatele. Vyvolává přes HTTP, frontu úložiště, frontu sběrnice nebo téma sběrnice.

Scheduler plánuje [úlohy](scheduler-concepts-terms.md) uchovává historii výsledků provedení úloh, která se dá zobrazit, a deterministicky a spolehlivě plánuje spouštění úloh. Azure WebJobs (součást funkce Web Apps v Azure App Service) a další plánovací nástroje Azure používají Scheduler na pozadí. [REST API Scheduleru ](https://msdn.microsoft.com/library/mt629143.aspx) pomáhá spravovat komunikaci pro tyto akce. Scheduler jako takový bez problémů podporuje [komplexní plánování a pokročilé opakování](scheduler-advanced-complexity.md).

Použití Scheduleru se samo nabízí v několika situacích. Příklad:

* *Opakující se akce aplikace:* Pravidelné shromažďování dat z Twitteru do informačního kanálu.
* *Každodenní údržba:* Každodenní vyřazování protokolů, zálohování a další úkoly týkající se údržby. Správce se například může rozhodnout zálohovat databáze v 1:00 v noci každý den dalších devět měsíců.

Scheduler umožní vytvářet, aktualizovat, odstraňovat, zobrazovat a spravovat úlohy a [kolekce úloh](scheduler-concepts-terms.md) programově pomocí skriptů a pomocí portálu.

## <a name="see-also"></a>Viz také
 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [Sestavení komplexních plánů a pokročilé opakování v Azure Scheduleru](scheduler-advanced-complexity.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

