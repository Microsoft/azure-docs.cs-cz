---
title: Virtual Machines osvědčené postupy pro Azure automanage
description: Seznamte se s osvědčenými postupy pro služby, které jsou automaticky připojené a nakonfigurované pro virtuální počítače v Azure automanage.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 001721ac0fdbbaaf808b962a79353369ab1dddb5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521950"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Osvědčené postupy pro virtuální počítače v Azure automanage


Tyto služby Azure se automaticky přiřadí za vás, když používáte automatickou správu virtuálních počítačů. Jsou zásadní pro náš dokument white paper k osvědčeným postupům, který najdete v našem [cloudovém rozhraní pro přijetí](/azure/cloud-adoption-framework/manage/azure-server-management).

U všech těchto služeb se automaticky připojíte, automaticky nakonfigurujete, sledujete a nasadíte, pokud se zjistí posun. Další informace o tomto procesu najdete v tématu [Azure automanage pro virtuální počítače](automanage-virtual-machines.md).


## <a name="participating-services"></a>Zúčastněné služby

|Služba    |Popis    |Podporované profily<sup>1</sup>    |Podporované předvolby<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorování virtuálních počítačů v Insights    |Azure Monitor pro virtuální počítače monitoruje výkon a stav virtuálních počítačů, včetně jejich spuštěných procesů a závislostí na jiných prostředcích. [Další](../azure-monitor/insights/vminsights-overview.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní    |Ne    |
|Backup    |Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před nezamýšleným zničením dat na virtuálních počítačích. [Další](../backup/backup-azure-vms-introduction.md)informace Poplatky vycházejí z počtu a velikosti chráněných virtuálních počítačů. [Další](https://azure.microsoft.com/pricing/details/backup/)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní    |Ano    |
|Azure Security Center    |Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu. [Další](../security-center/security-center-introduction.md)informace  Při této službě se bude konfigurovat předplatné, ve kterém se virtuální počítač nachází, do nabídky Azure Security Center úrovně Free. Pokud je vaše předplatné už Azure Security Centeré, nebude se při autosprávě znovu konfigurovat.    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |
|Antimalware od Microsoftu    |Microsoft Antimalware pro Azure je bezplatná ochrana v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Vygeneruje výstrahy v případě, že se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v systémech Azure. [Další](../security/fundamentals/antimalware.md)informace |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ano    |
|Update Management    |Pomocí Update Management v Azure Automation můžete spravovat aktualizace operačního systému pro virtuální počítače. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery. [Další](../automation/update-management/update-mgmt-overview.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |
|Inventář Change Tracking &    |Change Tracking a inventář kombinuje funkce Change Tracking a Inventory, které vám umožní sledovat změny infrastruktury virtuálních počítačů a serverů. Služba podporuje sledování změn napříč službami, démony softwaru, registru a souborů ve vašem prostředí, které vám pomůžou diagnostikovat nechtěné změny a vyvolat výstrahy. Podpora inventáře vám umožní dotazovat se na prostředky v hostovi pro přehlednost v nainstalovaných aplikacích a dalších položkách konfigurace.  [Další](../automation/change-tracking/overview.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |
|Konfigurace hosta Azure    | Zásada konfigurace hosta slouží k monitorování konfigurace a hlášení o dodržování předpisů počítače. Služba automanage nainstaluje [základní hodnoty zabezpečení systému Windows](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) pomocí rozšíření konfigurace hosta. [Další](../governance/policy/concepts/guest-configuration.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |
|Účet Azure Automation    |Azure Automation podporuje správu v celém životním cyklu vaší infrastruktury a aplikací. [Další](../automation/automation-intro.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |
|Pracovní prostor Log Analytics    |Azure Monitor ukládá data protokolu v pracovním prostoru Log Analytics, což je prostředek Azure a kontejner, ve kterém se data shromažďují, agreguje a slouží jako hranice správy. [Další](../azure-monitor/platform/design-logs-deployment.md)informace    |Osvědčené postupy pro virtuální počítače Azure – provozní, osvědčené postupy pro virtuální počítače Azure – vývoj a testování    |Ne    |


<sup>1</sup> konfigurační profily jsou k dispozici, když povolujete možnost automanage. [Další](automanage-virtual-machines.md#configuration-profiles)informace Můžete také upravit výchozí nastavení konfiguračního profilu a nastavit vlastní předvolby v rámci omezení osvědčených postupů.


## <a name="next-steps"></a>Další kroky

Zkuste povolit automanage pro virtuální počítače v Azure Portal.

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)