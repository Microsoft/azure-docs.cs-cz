---
title: Vytvořit konfigurace z existujících serverů – Azure Automation
description: Naučte se vytvářet konfigurace z existujících serverů pro Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 35f967e946854c3ca097db379015a7ee0bbe2f3d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231692"
---
# <a name="create-configurations-from-existing-servers"></a>Vytvoření konfigurací z existujících serverů

> Platí pro: Windows PowerShell 5,1

Vytváření konfigurací z existujících serverů může být náročný úkol.
Možná nebudete chtít *všechna* nastavení, jenom ty, které vás zajímají.
A pak potřebujete znát, v jakém pořadí se musí nastavení použít, aby se konfigurace úspěšně používala.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-reversedsc"></a>Projekt komunity: ReverseDSC

Pro práci v této oblasti od služby SharePoint bylo vytvořeno řešení udržované komunitou s názvem [ReverseDSC](https://github.com/microsoft/reversedsc) .

Řešení sestaví na [prostředku SharePointDSC](https://github.com/powershell/sharepointdsc) a rozšiřuje ho, aby orchestroval [shromažďování informací](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) z existujících serverů SharePoint.
Nejnovější verze obsahuje několik [režimů extrakce](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) , které určují, jaké úrovně informací se mají zahrnout.

Výsledkem použití řešení je generování [konfiguračních dat](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) , která se mají použít s konfiguračními skripty pro SharePointDSC.

Po vygenerování datových souborů je můžete použít s [konfiguračními skripty DSC](/powershell/scripting/dsc/overview/overview) k vygenerování souborů MOF a [nahrání souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet ReverseDSC, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Další kroky

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Prostředky DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
