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
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559378"
---
# <a name="configuration-data-at-scale"></a>Škálovaná konfigurační data

> Platí pro: Windows PowerShell 5.1

Správa stovek nebo tisíců serverů může být výzvou.
Zákazníci získali zpětnou vazbu, že nejobtížnější aspekt skutečně spravuje [konfigurační data](/powershell/dsc/configurations/configdata).
Uspořádání informací v rámci logických konstrukcí, jako je umístění, typ a prostředí.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-datum"></a>Komunitní projekt: Datum

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

Po vygenerování datových souborů je můžete použít s [konfiguračními skripty DSC](/powershell/dsc/configurations/write-compile-apply-configuration) k vygenerování souborů MOF a nahrání [souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet datum, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/datum/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Další postup

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/dsc/overview/overview)
- [Prostředky DSC](/powershell/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
