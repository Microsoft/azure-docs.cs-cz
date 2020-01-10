---
title: Řešení potíží s Azure Change Tracking
description: Naučte se řešit problémy a řešit potíže s funkcí Azure Automation Change Tracking a inventáře.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 51a9dbf8be6538534c05a4b8b6fcd913ef8c6ae3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769927"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Řešení potíží s řešeními Change Tracking a Inventory

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scénář: u počítačů s Windows se nezobrazuje Change Trackingé záznamy

#### <a name="issue"></a>Problém

Pro počítače s Windows, které jsou zaregistrované pro Change Tracking, se nezobrazuje žádné inventáře ani výsledky Change Tracking.

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena následujícími důvody:

1. **Microsoft Monitoring Agent** neběží.
2. Komunikace zpět s účtem Automation je zablokovaná.
3. Sady Management Pack pro Change Tracking se nestahují.
4. Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovanou Microsoft Monitoring Agent.

#### <a name="resolution"></a>Rozlišení

1. Ověřte, že na tomto počítači je spuštěný **Microsoft Monitoring Agent** (HealthService. exe).
1. Zkontrolujte **Prohlížeč událostí** na počítači a vyhledejte všechny události, které mají slovo `changetracking`.
1. Navštivte téma [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) , kde se dozvíte, které adresy a porty je potřeba povolit, aby Change Tracking fungovalo.
1. Ověřte, že místně existují následující Change Tracking a sady Management Pack inventáře:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Pokud používáte Klonovaný obrázek, nejprve na něj nainstalujte nástroj Sysprep a nainstalujte agenta Microsoft Monitoring Agent.

Pokud tato řešení problém nevyřeší a kontaktuje podporu, můžete spuštěním následujících příkazů shromáždit diagnostiku v agentovi.

Na počítači agenta přejděte na `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Pokud chcete povolit podrobné chybové zprávy jako v předchozím příkladu, je ve výchozím nastavení povoleno trasování chyb. použijte `VER` parametr. Pro trasování informací použijte `INF` při vyvolání `StartTracing.cmd`.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
