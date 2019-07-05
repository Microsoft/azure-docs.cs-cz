---
title: Azure Automation a Log Analytics mapování pracovního prostoru
description: Tento článek popisuje mapování povolené mezi účet Automation a pracovního prostoru Log Analytics pro podporu řešení
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478584"
---
# <a name="workspace-mappings"></a>Mapování pracovního prostoru

Při povolování řešení, jako jsou Update Management, řešení Change Tracking a Inventory nebo spouštění/zastavování virtuálních počítačů špičku, pro propojení pracovního prostoru Log Analytics a účet Automation se podporují pouze určité oblasti. Toto mapování se vztahuje pouze na účtu Automation a pracovního prostoru Log Analytics. Prostředky odesílajících sestavy do pracovního prostoru účtu Automation a Log Analytics může být v jiných oblastech.

## <a name="supported-mappings"></a>Podporované mapování

V následující tabulce jsou uvedeny podporované mapování:

|**Oblasti pracovního prostoru log Analytics**|**Oblasti služby Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asie a Tichomoří**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Evropa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**USA (gov)**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS mapování pracovních prostorů Log Analytics pro účty Automation není přesný mapování oblastmi, ale je správné mapování.

<sup>2</sup> z důvodu omezení kapacity oblast není k dispozici, při vytváření nových prostředků. To zahrnuje pracovní prostory účty Automation a Log Analytics. Dříve existující propojených prostředků v oblasti však musí i nadále fungovat.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že již nechcete integrace účtu služby Automation s pracovním prostorem Log Analytics, můžete svůj účet neodpojíte přímo z portálu Azure portal. Než budete pokračovat, musíte nejprve odebrat Update Management, řešení Change Tracking a Inventory nebo spouštění/zastavování virtuálních počítačů během řešení době mimo špičku, pokud je používáte. Pokud je neodstraníte, tento proces se zakáže řízení. Přečtěte si článek pro konkrétní řešení, které jste importovali pochopit kroky nutné k jeho odebrání.

Po odebrání těchto řešení provedením následujících kroků se zrušit propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzí řešení monitorování Azure SQL pravděpodobně vytvořena prostředky služby automation a může také muset před rušení propojení pracovního prostoru odebrat.

1. Z portálu Azure portal otevřete svůj účet Automation, a na automatizaci účtu vyberte stránku **pracovní prostor propojený** části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na tlačítko **zrušit propojení pracovního prostoru**. Zobrazí výzva s dotazem, jestli že chcete pokračovat.

3. Zatímco bude Azure Automation se pokusí zrušit propojení účtu pracovního prostoru Log Analytics, můžete sledovat průběh **oznámení** z nabídky.

Pokud jste použili řešení Update Management, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Plány aktualizace – každá bude mít názvy, které odpovídají vámi vytvořených nasazení aktualizací)

* Skupiny hybridních pracovních procesů vytvořené pro dané řešení – každá bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spouštění/zastavování virtuálních počítačů špičku, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Spouštět a zastavovat sady runbook plány virtuálního počítače
* Spuštění a zastavení sad runbook virtuálního počítače
* Proměnné

Také je můžete také zrušit propojení pracovního prostoru ve svém účtu Automation z pracovního prostoru Log Analytics. Ve svém pracovním prostoru, vyberte **účtu Automation** pod **související prostředky**. Na stránce účtu Automation vyberte **zrušení propojení účtu**.

## <a name="next-steps"></a>Další postup

Zjistěte, jak se zapojit následujících řešení:

Update Management a Change Tracking a Inventory:

* Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md)
* Z vaší [účtu Automation](../automation-onboard-solutions-from-automation-account.md)
* Když [procházení více počítačů](../automation-onboard-solutions-from-browse.md)
* Z [sady runbook](../automation-onboard-solutions.md)

Spuštění/zastavení virtuálních počítačů mimo špičku

* [Spouštění/zastavování virtuálních počítačů nasadit špičku](../automation-solution-vm-management.md)