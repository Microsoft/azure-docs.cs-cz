---
title: Konfigurační data ve velkém měřítku – Azure Automation
description: Zjistěte, jak nakonfigurovat data ve velkém měřítku pro konfiguraci stavu v Azure Automation.
keywords: dsc,powershell,konfigurace,nastavení
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028299"
---
# <a name="configuration-data-at-scale"></a>Konfigurace dat ve velkém měřítku

> Platí pro: Windows PowerShell 5.1

Správa stovek nebo tisíců serverů může být výzvou.
Zákazníci poskytli zpětnou vazbu, že nejtěžším aspektem je skutečně správa [konfiguračních dat](/powershell/scripting/dsc/configurations/configdata).
Uspořádání informací napříč logickými konstrukcemi, jako je umístění, typ a prostředí.

> [!NOTE]
> Tento článek odkazuje na řešení, které je udržováno komunitou open source.
> Podpora je k dispozici pouze ve formě spolupráce githubu, ne od Microsoftu.

## <a name="community-project-datum"></a>Komunitní projekt: Datum

K vyřešení této výzvy bylo vytvořeno komunitní řešení s názvem [Datum.](https://github.com/gaelcolas/Datum)
Datum staví na skvělých nápadech z jiných platforem pro správu konfigurace a implementuje stejný typ řešení pro Prostředí PowerShell DSC.
Informace jsou [uspořádány do textových souborů](https://github.com/gaelcolas/Datum#3-intended-usage) na základě logických nápadů.
Příkladem může být:

- Nastavení, která by měla platit globálně
- Nastavení, která by měla platit pro všechny servery v umístění
- Nastavení, která by měla platit pro všechny databázové servery
- Nastavení jednotlivých serverů

Tyto informace jsou uspořádány ve formátu souboru, který dáváte přednost (JSON, Yaml nebo PSD1).
Poté jsou k dispozici rutiny pro generování konfiguračních datových souborů [konsolidací informací](https://github.com/gaelcolas/Datum#datum-tree) z každého souboru do jednoho zobrazení role serveru nebo serveru.

Po vygenerování datových souborů je můžete použít se [skripty Konfigurace DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) ke generování souborů MOF a [nahrání souborů MOF do azure automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak zaregistrujte své servery z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) pro vyžádat konfigurace.

Chcete-li vyzkoušet datum, navštivte [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/datum/) a stáhněte si řešení nebo klikněte na "Web projektu" a prohlédněte si [dokumentaci](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Další kroky

- [Windows PowerShell – přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview)
- [Zdroje dsc](/powershell/scripting/dsc/resources/resources)
- [Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaconfig)
