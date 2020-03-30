---
title: Vytváření konfigurací z existujících serverů – Azure Automation
description: Zjistěte, jak vytvořit konfigurace z existujících serverů pro Azure Automation.
keywords: dsc,powershell,konfigurace,nastavení
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030121"
---
# <a name="create-configurations-from-existing-servers"></a>Vytvoření konfigurací z existujících serverů

> Platí pro: Windows PowerShell 5.1

Vytváření konfigurací z existujících serverů může být náročný úkol.
Možná nebudete chtít *všechna* nastavení, jen ta, na kterých vám záleží.
I tak musíte vědět, v jakém pořadí musí být nastavení použito, aby se konfigurace úspěšně použila.

> [!NOTE]
> Tento článek odkazuje na řešení, které je udržováno komunitou open source.
> Podpora je k dispozici pouze ve formě spolupráce githubu, ne od Microsoftu.

## <a name="community-project-reversedsc"></a>Komunitní projekt: ReverseDSC

Pro práci v této oblasti, která se spouští se SharePointem, bylo vytvořeno komunitní řešení s názvem [ReverseDSC.](https://github.com/microsoft/reversedsc)

Řešení staví na [prostředku SharePointDSC](https://github.com/powershell/sharepointdsc) a rozšiřuje jej tak, aby organizoval [shromažďování informací](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) z existujících serverů SharePoint.
Nejnovější verze má více [režimů extrakce](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) k určení, jakou úroveň informací zahrnout.

Výsledkem použití řešení je generování [konfiguračních dat](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) pro použití s konfiguračními skripty SharePointDSC.

Po vygenerování datových souborů je můžete použít se [skripty Konfigurace DSC](/powershell/scripting/dsc/overview/overview) ke generování souborů MOF a [nahrání souborů MOF do azure automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak zaregistrujte své servery z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) pro vyžádat konfigurace.

Chcete-li vyzkoušet ReverseDSC, navštivte [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) a stáhněte si řešení nebo klikněte na "Projektový web" a prohlédněte si [dokumentaci](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Další kroky

- [Windows PowerShell – přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview)
- [Zdroje dsc](/powershell/scripting/dsc/resources/resources)
- [Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaconfig)
