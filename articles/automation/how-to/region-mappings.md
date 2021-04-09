---
title: Podporované oblasti pro propojený pracovní prostor Log Analytics
description: Tento článek popisuje podporovaná mapování oblastí mezi účtem služby Automation a pracovním prostorem Log Analytics v souvislosti s některými funkcemi Azure Automation.
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720167"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Podporované oblasti pro propojený pracovní prostor Log Analytics

V Azure Automation můžete povolit Update Management, Change Tracking a inventář a Start/Stop VMs during off-hours funkce pro vaše servery a virtuální počítače. Tyto funkce mají závislost na pracovním prostoru Log Analytics, a proto vyžadují propojení pracovního prostoru s účtem Automation. Pouze některé oblasti jsou však podporovány, aby je bylo možné propojit dohromady. Mapování se obecně *nedá* použít, pokud plánujete propojit účet Automation s pracovním prostorem, který tyto funkce nemá povolený.

Zde popsané mapování se vztahuje pouze na propojení pracovního prostoru Log Analytics k účtu Automation. Nevztahují se na virtuální počítače, které jsou připojené k pracovnímu prostoru, který je propojený s účtem Automation. Virtuální počítače nejsou omezené na oblasti podporované daným Log Analytics pracovním prostorem. Můžou být v libovolné oblasti. Mějte na paměti, že virtuální počítače v jiné oblasti mohou ovlivnit požadavky na stav, místní a země nebo požadavky na dodržování předpisů vaší společnosti. Pokud máte virtuální počítače v jiné oblasti, můžete také účtovat poplatky za šířku pásma dat.

Před připojením virtuálních počítačů k pracovnímu prostoru v jiné oblasti byste si měli projít požadavky a potenciální náklady na potvrzení a pochopení právního a nákladového dopadu.

Tento článek obsahuje podporovaná mapování, aby bylo možné úspěšně povolit a používat tyto funkce ve vašem účtu Automation.

Další informace najdete v tématu [Log Analytics pracovní prostor a účet Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Podporovaná mapování

> [!NOTE]
> Jak je znázorněno v následující tabulce, může mezi Log Analytics a Azure Automation existovat pouze jedno mapování.

V následující tabulce jsou uvedena podporovaná mapování:

|**Oblast pracovního prostoru Log Analytics**|**Oblast Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asie a Tichomoří**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Evropa**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> mapování EastUS pro Log Analytics pracovní prostory na účty Automation není přesné mapování oblastí na oblast, ale je to správné mapování.

<sup>2</sup> mapování EastUS2 pro Log Analytics pracovní prostory na účty Automation není přesné mapování oblastí na oblast, ale jedná se o správné mapování.

<sup>3</sup> v této oblasti je podporovaná jenom Update Management a další funkce, jako je Change Tracking a inventář, nejsou v tuto chvíli k dispozici.

## <a name="unlink-a-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejdřív [odebrat](move-account.md#remove-features) Update Management, Change Tracking a inventář a Start/Stop VMS during off-hours, pokud je používáte. Pokud je neodeberete, nemůžete dokončit operaci odpojování.

Po odebrání funkcí můžete pomocí následujících kroků zrušit propojení svého účtu Automation.

> [!NOTE]
> Některé funkce, včetně dřívějších verzí řešení Azure SQL monitoring, mohly vytvořit assety automatizace, které je potřeba před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation. Na stránce účet Automation vyberte v části **související prostředky** možnost **propojený pracovní prostor** .

2. Na stránce zrušit propojení pracovního prostoru vyberte zrušit **propojení pracovního prostoru**. Zobrazí se výzva, abyste ověřili, jestli chcete pokračovat.

3. I když Azure Automation odpojíte účet z pracovního prostoru Log Analytics, můžete sledovat průběh v nabídce **oznámení** .

4. Pokud jste použili Update Management, můžete taky chtít odebrat následující položky, které už nepotřebujete:

    * Plány aktualizací: každý má název, který odpovídá nasazení aktualizace, kterou jste vytvořili.
    * Skupiny hybridních pracovních procesů vytvořené pro funkci: každý má název podobný  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Pokud jste použili Start/Stop VMs during off-hours, můžete volitelně odebrat následující položky, které už nepotřebujete:

    * Spuštění a zastavení plánů Runbook VM
    * Spuštění a zastavení runbooků virtuálních počítačů
    * Proměnné

Alternativně můžete zrušit propojení pracovního prostoru s účtem Automation v pracovním prostoru.

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.
2. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o Update Management v článku [Update Management přehled](../update-management/overview.md).
* Přečtěte si o Change Tracking a inventáři v [přehledu Change Tracking a inventáře](../change-tracking/overview.md).
* Přečtěte si o Start/Stop VMs during off-hours v článku [Start/Stop VMS during off-hours přehled](../automation-solution-vm-management.md).