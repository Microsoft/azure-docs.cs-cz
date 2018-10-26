---
title: Řešení potíží s Azure Change Tracking
description: Tento článek obsahuje informace o řešení Change Tracking
services: automation
ms.service: automation
ms.component: change-tracking
author: georgewallace
ms.author: gwallace
ms.date: 10/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f2e66a1fab31ce6099ab426d6e8ce144e155efb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088836"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Řešení Change Tracking a Inventory

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scénář: Záznamy řešení Change Tracking nejsou zobrazeny na webu Azure Portal

#### <a name="issue"></a>Problém

Nevidíte žádné výsledky inventáře a sledování změn pro počítače, které jsou připojili pro řešení Change Tracking.

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. **Agenta Microsoft Monitoring Agent** neběží.
2. Blokuje komunikaci zpět do účtu Automation.
3. Sady Management Pack pro řešení Change Tracking, se nestáhnou.
4. Virtuální počítač právě připojili může pocházet z klonovaného počítače, který nebyl Sysprep s nainstalovaným agentem sledování Microsoft.

#### <a name="resolution"></a>Řešení

1. Ověřte, **agenta Microsoft Monitoring Agent** (HealthService.exe) běží na počítači.
2. Navštíví, [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) Další informace o tom, které adresy a porty je potřeba povolit pro řešení Change Tracking pracovat.
3. Ověřte, že tyto management packy Change Tracking a Inventory existují místně:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
4. Pokud pomocí nejprve image něm Klonovaná image nástroj sysprep a nainstalovat agenta Microsoft Monitoring Agent po jejich výskytu.

Pokud budete kontaktovat podporu těchto řešení není problém vyřešit, spustíte následující příkazy ke shromažďování diagnostiky na agentovi

Na počítači agenta, přejděte na `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` a spusťte následující příkazy:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Ve výchozí chyba je povoleno trasování, pokud chcete povolit podrobné chybové zprávy jako v předchozím příkladu, použijte `VER` parametru. Informace o trasování, použijte `INF` při vyvolání `StartTracing.cmd`.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.