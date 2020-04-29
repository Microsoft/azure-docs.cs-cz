---
title: Konfigurační data ve velkém měřítku – Azure Automation
description: Naučte se konfigurovat škálovatelná data pro konfiguraci stavu v Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585560"
---
# <a name="configuration-data-at-scale"></a>Konfigurace dat ve velkém měřítku

> Platí pro: Windows PowerShell 5,1

Správa stovek nebo tisíců serverů může být výzvou.
Zákazníci získali zpětnou vazbu, že nejobtížnější aspekt skutečně spravuje [konfigurační data](/powershell/scripting/dsc/configurations/configdata).
Uspořádání informací v rámci logických konstrukcí, jako je umístění, typ a prostředí.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-datum"></a>Komunitní projekt: datum

Pro vyřešení této výzvy bylo vytvořeno řešení udržované komunitou s názvem [Datum](https://github.com/gaelcolas/Datum) .
Datum se staví na skvělých nápadech z jiných platforem správy konfigurace a implementuje stejný typ řešení pro prostředí PowerShell DSC.
Informace jsou [uspořádány do textových souborů](https://github.com/gaelcolas/Datum#3-intended-usage) na základě logických nápadů.
Příkladem může být:

- Nastavení, která by se měla použít globálně
- Nastavení, která by se měla použít na všechny servery v umístění
- Nastavení, která by se měla použít na všechny databázové servery
- Individuální nastavení serveru

Tyto informace jsou uspořádány ve formátu souboru, který dáváte přednost (JSON, YAML nebo PSD1).
Pak jsou k dispozici rutiny pro generování datových souborů konfigurace tím, že [konsolidují informace](https://github.com/gaelcolas/Datum#datum-tree) z každého souboru do jednoho zobrazení role serveru nebo serveru.

Po vygenerování datových souborů je můžete použít s [konfiguračními skripty DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) k vygenerování souborů MOF a [nahrání souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet datum, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/datum/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Další kroky

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Prostředky DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
