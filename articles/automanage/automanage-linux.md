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
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863060"
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
|[Monitorování virtuálních počítačů v Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor pro virtuální počítače monitoruje výkon a stav virtuálních počítačů, včetně jejich spuštěných procesů a závislostí na jiných prostředcích. Další [informace](../azure-monitor/vm/vminsights-overview.md)    |Výroba    |No    |
|[Backup](https://docs.microsoft.com/azure/backup/backup-overview)   |Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před nezamýšleným zničením dat na virtuálních počítačích. Další [informace](../backup/backup-azure-vms-introduction.md) Poplatky vycházejí z počtu a velikosti chráněných virtuálních počítačů. Další [informace](https://azure.microsoft.com/pricing/details/backup/)    |Výroba    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu. Další [informace](../security-center/security-center-introduction.md)  Při této službě se bude konfigurovat předplatné, ve kterém se virtuální počítač nachází, do nabídky Azure Security Center úrovně Free. Pokud je vaše předplatné již Azure Security Center, nebude ho znovu konfigurovat.    |Výroba, vývoj a testování    |No    |
|[Správa aktualizací](https://docs.microsoft.com/azure/automation/update-management/overview)    |Pomocí Update Management v Azure Automation můžete spravovat aktualizace operačního systému pro virtuální počítače. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery. Další [informace](../automation/update-management/overview.md)    |Výroba, vývoj a testování    |No    |
|[Inventář Change Tracking &](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Change Tracking a inventář kombinuje funkce Change Tracking a Inventory, které vám umožní sledovat změny infrastruktury virtuálních počítačů a serverů. Služba podporuje sledování změn napříč službami, démony softwaru, registru a souborů ve vašem prostředí, které vám pomůžou diagnostikovat nechtěné změny a vyvolat výstrahy. Podpora inventáře vám umožní dotazovat se na prostředky v hostovi pro přehlednost v nainstalovaných aplikacích a dalších položkách konfigurace.  Další [informace](../automation/change-tracking/overview.md)    |Výroba, vývoj a testování    |No    |
|[Konfigurace hosta Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | Zásada konfigurace hosta slouží k monitorování konfigurace a hlášení o dodržování předpisů počítače. Služba automanage nainstaluje směrný plán Azure Linux pomocí rozšíření konfigurace hosta. V případě počítačů se systémem Linux nainstaluje služba konfigurace hosta standardní hodnotu v režimu jenom pro auditování. Budete moct zjistit, kde se váš virtuální počítač neshoduje se směrným plánem, ale nedodržování předpisů se nebude automaticky opravovat. Další [informace](../governance/policy/concepts/guest-configuration.md)    |Výroba, vývoj a testování    |No    |
|[Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | Diagnostika spouštění je funkce ladění pro virtuální počítače Azure (VM), která umožňuje diagnostiku selhání při spouštění virtuálních počítačů. Diagnostika spouštění umožňuje uživateli sledovat stav svého virtuálního počítače, protože se spouští shromažďováním informací o sériovém protokolu a snímků obrazovky. Tato možnost bude povolená jenom pro počítače, které používají spravované disky. |Výroba, vývoj a testování    |No    |
|[Účet Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation podporuje správu v celém životním cyklu vaší infrastruktury a aplikací. Další [informace](../automation/automation-intro.md)    |Výroba, vývoj a testování    |No    |
|[Pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor ukládá data protokolu v pracovním prostoru Log Analytics, což je prostředek Azure a kontejner, ve kterém se data shromažďují, agreguje a slouží jako hranice správy. Další [informace](../azure-monitor/logs/design-logs-deployment.md)    |Výroba, vývoj a testování    |No    |


<sup>1</sup> výběr prostředí je dostupný, když povolíte možnost automanage. Další [informace](automanage-virtual-machines.md#environment-configuration) Můžete také upravit výchozí nastavení prostředí a nastavit vlastní preference v rámci omezení osvědčených postupů.


## <a name="next-steps"></a>Další kroky

Zkuste povolit automanage pro virtuální počítače v Azure Portal.

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)