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
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849509"
---
# <a name="workspace-mappings"></a>Mapování pracovních prostorů

Při povolení řešení, jako je správa aktualizací, sledování změn a inventář nebo spuštění a zastavení virtuálních účtů během mimopracovní dobu řešení, jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu automatizace. Toto mapování platí pouze pro účet automatizace a pracovní prostor Analýzy protokolů. Prostředky, které se hlásí do vašeho účtu automation nebo pracovního prostoru Log Analytics, se mohou nasytit v jiných oblastech.

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

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete svůj účet odpojit přímo z portálu Azure. Než budete pokračovat, musíte nejprve odebrat řešení Probíhá správa aktualizací, Sledování změn a inventář e nebo virtuální chodstart/stop během mimopracovní doby. Pokud je neodstraníte, bude tento proces znemožněn. Projděte si článek konkrétního řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky k odpojení účtu automatizace.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení monitorování Azure SQL může mít vytvořené prostředky automatizace a může být také nutné odebrat před odpojením pracovního prostoru.

1. Na webu Azure Portal otevřete účet Automation a na stránce Účet automatizace vyberte **Propojený pracovní prostor** v části Související **prostředky** na levé straně.

2. Na stránce Odpojit od propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**. Zobrazí se výzva, zda chcete pokračovat.

3. Zatímco Azure Automation se pokusí odpojit účet vašeho pracovního prostoru Log Analytics, můžete sledovat průběh v části **Oznámení** z nabídky.

Pokud jste použili řešení správy aktualizací, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Plány aktualizací – každý bude mít názvy, které odpovídají vytvořeným nasazením aktualizací)

* Hybridní skupiny pracovních pracovníků vytvořené pro řešení `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`– každý bude pojmenován podobně jako ).

Pokud jste použili spuštění a zastavení virtuálních her během mimo pracovní dobu řešení, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Spuštění a zastavení plánů runbooku virtuálních knih
* Spuštění a zastavení runbooků virtuálních knih
* Proměnné

Případně můžete také odpojit pracovní prostor od účtu Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet automatizace** v části **Související zdroje**. Na stránce Automation Account vyberte **Možnost Zrušit propojení účtu**.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak se můžete seznámit s následujícími řešeními:

Aktualizovat správu a sledování změn a inventuru:

* Z [virtuálního počítače](../automation-onboard-solutions-from-vm.md)
* Z vašeho [účtu Automation](../automation-onboard-solutions-from-automation-account.md)
* Při [procházení více strojů](../automation-onboard-solutions-from-browse.md)
* Z [runbooku](../automation-onboard-solutions.md)

Spuštění/zastavení virtuálních počítačů mimo špičku

* [Nasazení virtuálních virtuálních discích Start/Stop mimo pracovní dobu](../automation-solution-vm-management.md)
