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
ms.openlocfilehash: b8c39ba6c12d43da1b2311ae4d7d85dd13946f25
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559465"
---
# <a name="create-configurations-from-existing-servers"></a>Vytvoření konfigurací z existujících serverů

> Platí pro: Windows PowerShell 5.1

Vytváření konfigurací z existujících serverů může být náročný úkol.
Možná nebudete chtít *všechna* nastavení, jenom ty, které vás zajímají.
A pak potřebujete znát, v jakém pořadí se musí nastavení použít, aby se konfigurace úspěšně používala.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-reversedsc"></a>Komunitní projekt: ReverseDSC

Pro práci v této oblasti od služby SharePoint bylo vytvořeno řešení udržované komunitou s názvem [ReverseDSC](https://github.com/microsoft/reversedsc) .

Řešení sestaví na [prostředku SharePointDSC](https://github.com/powershell/sharepointdsc) a rozšiřuje ho, aby orchestroval [shromažďování informací](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) z existujících serverů SharePoint.
Nejnovější verze obsahuje několik [režimů extrakce](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) , které určují, jaké úrovně informací se mají zahrnout.

Výsledkem použití řešení je generování [konfiguračních dat](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) , která se mají použít s konfiguračními skripty pro SharePointDSC.

Po vygenerování datových souborů je můžete použít s [konfiguračními skripty DSC](/powershell/dsc/overview/overview) k vygenerování souborů MOF a nahrání [souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet ReverseDSC, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Další kroky

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/dsc/overview/overview)
- [Prostředky DSC](/powershell/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
