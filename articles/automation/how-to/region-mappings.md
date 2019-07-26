---
title: Mapování pracovního prostoru Azure Automation a Log Analytics
description: Tento článek popisuje mapování povolená mezi účtem služby Automation a pracovním prostorem Log Analytics pro podporu řešení.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498374"
---
# <a name="workspace-mappings"></a>Mapování pracovních prostorů

Při povolování řešení, jako je Update Management, Change Tracking a inventář nebo spuštění/zastavení virtuálních počítačů v době mimo špičku, jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Toto mapování platí pouze pro účet Automation a pracovní prostor Log Analytics. Prostředky, které se hlásí do vašeho účtu Automation nebo do pracovního prostoru Log Analytics, můžou být v jiných oblastech.

## <a name="supported-mappings"></a>Podporovaná mapování

V následující tabulce jsou uvedena podporovaná mapování:

|**Oblast pracovního prostoru Log Analytics**|**Oblast Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asie a Tichomoří**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Evropa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> mapování EastUS pro Log Analytics pracovní prostory do účtů Automation není přesné mapování oblastí na oblast, ale je to správné mapování.

<sup>2</sup> z důvodu omezení kapacity není oblast při vytváření nových prostředků dostupná. To zahrnuje účty Automation a pracovní prostory Log Analytics. Stávající propojené prostředky v oblasti ale budou fungovat i nadále.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejdřív odebrat Update Management, Change Tracking a inventář nebo spustit nebo zastavit virtuální počítače v době mimo špičku, pokud je používáte. Pokud je neodeberete, proces nebude moci pokračovat. Projděte si článek pro konkrétní řešení, které jste naimportovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky a zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvořit automatizační prostředky a možná je budete muset před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation a na stránce účet Automation vyberte **propojený pracovní prostor** v části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**. Zobrazí se výzva s dotazem, zda chcete pokračovat.

3. I když se Azure Automation pokusí odpojit účet Log Analytics pracovním prostoru, můžete sledovat průběh v nabídce **oznámení** .

Pokud jste použili řešení Update Management, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Aktualizace plánů – každá bude mít názvy, které odpovídají vytvořeným nasazením aktualizací.

* Skupiny hybridních pracovních procesů vytvořené pro řešení – každá bude pojmenována `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`podobně jako).

Pokud jste v době mimo špičku použili virtuální počítače spustit/zastavit, budete možná chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Spuštění a zastavení plánů Runbook VM
* Spuštění a zastavení runbooků virtuálních počítačů
* Proměnné

Alternativně můžete také zrušit propojení pracovního prostoru s účtem Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další postup

Naučte se, jak začlenit následující řešení:

Update Management a Change Tracking a inventář:

* Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md)
* Z [účtu Automation](../automation-onboard-solutions-from-automation-account.md)
* Při [procházení více počítačů](../automation-onboard-solutions-from-browse.md)
* Z [Runbooku](../automation-onboard-solutions.md)

Spuštění/zastavení virtuálních počítačů mimo špičku

* [Nasazení virtuálních počítačů spustit/zastavit v době mimo špičku](../automation-solution-vm-management.md)
