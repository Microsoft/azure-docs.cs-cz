---
title: Azure automanage pro Linux
description: Seznamte se s osvědčenými postupy pro službu Azure automanage pro virtuální počítače, které jsou automaticky připojené a nakonfigurované pro počítače se systémem Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 3aab43be49cb98fbe136e1f0216590785d650392
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953245"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Osvědčené postupy pro virtuální počítače Azure automanage – Linux

Tyto služby Azure se automaticky zaregistrují za vás při použití automatické správy pro virtuální počítače na virtuálním počítači se systémem Linux. Jsou zásadní pro náš dokument white paper k osvědčeným postupům, který najdete v našem [cloudovém rozhraní pro přijetí](/azure/cloud-adoption-framework/manage/azure-server-management).

U všech těchto služeb se automaticky připojíte, automaticky nakonfigurujete, sledujete a opravíte, pokud se zjistí posun. Další informace o tomto procesu najdete v tématu [Azure automanage pro virtuální počítače](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Podporované distribuce a verze systému Linux

Automanage podporuje následující distribuce a verze systému Linux:

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16,04 a 18,04
- SLES 12 (SP3 – pouze SP5)

## <a name="participating-services"></a>Zúčastněné služby

>[!NOTE]
> Microsoft Antimalware se v tuto chvíli nepodporuje u virtuálních počítačů se systémem Linux.

|Služba    |Popis    |Podporovaná prostředí<sup>1</sup>    |Podporované předvolby<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorování virtuálních počítačů v Insights    |Azure Monitor pro virtuální počítače monitoruje výkon a stav virtuálních počítačů, včetně jejich spuštěných procesů a závislostí na jiných prostředcích. [Další](../azure-monitor/vm/vminsights-overview.md)informace    |Výroba    |Ne    |
|Backup    |Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před nezamýšleným zničením dat na virtuálních počítačích. [Další](../backup/backup-azure-vms-introduction.md)informace Poplatky vycházejí z počtu a velikosti chráněných virtuálních počítačů. [Další](https://azure.microsoft.com/pricing/details/backup/)informace    |Výroba    |Ano    |
|Azure Security Center    |Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu. [Další](../security-center/security-center-introduction.md)informace  Při této službě se bude konfigurovat předplatné, ve kterém se virtuální počítač nachází, do nabídky Azure Security Center úrovně Free. Pokud je vaše předplatné již Azure Security Center, nebude ho znovu konfigurovat.    |Výroba, vývoj a testování    |Ne    |
|Update Management    |Pomocí Update Management v Azure Automation můžete spravovat aktualizace operačního systému pro virtuální počítače. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery. [Další](../automation/update-management/overview.md)informace    |Výroba, vývoj a testování    |Ne    |
|Inventář Change Tracking &    |Change Tracking a inventář kombinuje funkce Change Tracking a Inventory, které vám umožní sledovat změny infrastruktury virtuálních počítačů a serverů. Služba podporuje sledování změn napříč službami, démony softwaru, registru a souborů ve vašem prostředí, které vám pomůžou diagnostikovat nechtěné změny a vyvolat výstrahy. Podpora inventáře vám umožní dotazovat se na prostředky v hostovi pro přehlednost v nainstalovaných aplikacích a dalších položkách konfigurace.  [Další](../automation/change-tracking/overview.md)informace    |Výroba, vývoj a testování    |Ne    |
|Konfigurace hosta Azure    | Zásada konfigurace hosta slouží k monitorování konfigurace a hlášení o dodržování předpisů počítače. Služba automanage nainstaluje směrný plán Azure Linux pomocí rozšíření konfigurace hosta. V případě počítačů se systémem Linux nainstaluje služba konfigurace hosta standardní hodnotu v režimu jenom pro auditování. Budete moct zjistit, kde se váš virtuální počítač neshoduje se směrným plánem, ale nedodržování předpisů se nebude automaticky opravovat. [Další](../governance/policy/concepts/guest-configuration.md)informace    |Výroba, vývoj a testování    |Ne    |
|Účet Azure Automation    |Azure Automation podporuje správu v celém životním cyklu vaší infrastruktury a aplikací. [Další](../automation/automation-intro.md)informace    |Výroba, vývoj a testování    |Ne    |
|Pracovní prostor Log Analytics    |Azure Monitor ukládá data protokolu v pracovním prostoru Log Analytics, což je prostředek Azure a kontejner, ve kterém se data shromažďují, agreguje a slouží jako hranice správy. [Další](../azure-monitor/logs/design-logs-deployment.md)informace    |Výroba, vývoj a testování    |Ne    |


<sup>1</sup> výběr prostředí je dostupný, když povolíte možnost automanage. [Další](automanage-virtual-machines.md#environment-configuration)informace Můžete také upravit výchozí nastavení prostředí a nastavit vlastní preference v rámci omezení osvědčených postupů.


## <a name="next-steps"></a>Další kroky

Zkuste povolit automanage pro virtuální počítače v Azure Portal.

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)