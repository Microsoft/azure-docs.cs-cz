---
title: Mapování pracovních prostorů Azure Automation a Log Analytics
description: Tento článek popisuje mapování povolené mezi účtem Automation a pracovním prostorem Analýzy protokolů pro podporu řešení
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681841"
---
# <a name="workspace-mappings"></a>Mapování pracovních prostorů

Při povolení řešení, jako je správa aktualizací, sledování změn a inventář nebo spuštění a zastavení virtuálních účtů během mimo pracovní dobu, jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu automatizace. Toto mapování platí pouze pro účet Automatizace a pracovní prostor Analýzy protokolů. Prostředky vykazující váš účet Automation nebo pracovní prostor Log Analytics se mohou nasytit v jiných oblastech.

## <a name="supported-mappings"></a>Podporovaná mapování

V následující tabulce jsou uvedena podporovaná mapování:

|**Oblast pracovního prostoru analýzy protokolů**|**Oblast automatizace Azure**|
|---|---|
|**USA**||
|VýchodUS<sup>1</sup>|Východní US2|
|Západní US2|Západní US2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|KanadaStřední|KanadaStřední|
|**Asie a Tichomoří**||
|AustrálieJihovýchod|AustrálieJihovýchod|
|Jihovýchodní Asie|Jihovýchodní Asie|
|Střední Indie|Střední Indie|
|Japonskovýchod|Japonskovýchod|
|**Evropa**||
|Spojené královstvíJižní|Spojené královstvíJižní|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginie|USGovVirginie|

<sup>1</sup> EastUS mapování pro pracovní prostory Log Analytics na účty automatizace není přesné mapování oblasti k oblasti, ale je správné mapování.

<sup>2</sup> Vzhledem k omezením kapacity není oblast při vytváření nových zdrojů k dispozici. To zahrnuje účty automatizace a pracovní prostory Analýzy protokolů. Již existující propojené zdroje v regionu by však měly pokračovat v práci.

## <a name="unlink-workspace"></a>Zrušení propojení s pracovním prostorem

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete svůj účet odpojit přímo z portálu Azure. Než budete pokračovat, musíte nejprve odebrat řešení pro správu aktualizací, sledování změn a zásoby a spuštění a zastavení virtuálních min během mimo pracovní doby, pokud je používáte. Pokud je neodeberete, nelze dokončit operaci odpojení. Projděte si článek konkrétního řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky k odpojení účtu automatizace.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení monitorování Azure SQL může mít vytvořené prostředky automatizace a může být nutné odebrat před odpojením pracovního prostoru.

1. Na portálu Azure otevřete účet Automation. Na stránce Účet automatizace vyberte **Propojený pracovní prostor v** části Související **prostředky**.

2. Na stránce Odpojit od propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**. Pokud chcete pokračovat, zobrazí se výzva k ověření.

3. Zatímco Azure Automation se pokusí odpojit účet vašeho pracovního prostoru Log Analytics, můžete sledovat průběh v části **Oznámení** z nabídky.

4. Pokud jste použili řešení správy aktualizací, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

    * Plány aktualizací – každý má název, který odpovídá nasazení aktualizace, které jste vytvořili.
    * Hybridní skupiny pracovních pracovníků vytvořené pro `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`řešení – každý má název podobný .

5. Pokud jste použili start/stop virtuálních přivypnuto řešení mimo pracovní dobu, můžete volitelně odebrat následující položky, které nejsou potřeba po odebrání řešení.

    * Spuštění a zastavení plánů runbooku virtuálních knih
    * Spuštění a zastavení runbooků virtuálních knih
    * Proměnné

Případně můžete odpojit pracovní prostor od účtu Automatizace v rámci pracovního prostoru. 

1. V pracovním prostoru vyberte **účet automatizace** v části **Související zdroje**. 
2. Na stránce Automation Account vyberte **Možnost Zrušit propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak zavést řešení pro správu aktualizací a sledování změn a zásoby:

    * Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md)
    * Z vašeho [účtu Automation](../automation-onboard-solutions-from-automation-account.md)
    * Při [procházení více strojů](../automation-onboard-solutions-from-browse.md)
    * Z [runbooku](../automation-onboard-solutions.md)

* Přečtěte si, jak zavést řešení Start/Stop virtuálních počítačích mimo pracovní dobu:

    * [Nasazení virtuálních virtuálních discích Start/Stop mimo pracovní dobu](../automation-solution-vm-management.md)
