---
title: Konfigurace škálovatelných dat pro konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak nakonfigurovat škálovatelná data pro konfiguraci stavu Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f6773d9f2210fd1b0342c9586d89155082bf8ff
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711444"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Konfigurace škálovatelných dat pro konfiguraci stavu Azure Automation

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
