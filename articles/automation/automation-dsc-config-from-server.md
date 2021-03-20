---
title: Vytvoří konfigurace z existujících serverů pro konfiguraci stavu Azure Automation.
description: V tomto článku se dozvíte, jak vytvořit konfigurace z existujících serverů pro konfiguraci stavu Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86186499"
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

Po vygenerování datových souborů je můžete použít s [konfiguračními skripty DSC](/powershell/scripting/dsc/overview/overview) k vygenerování souborů MOF a [nahrání souborů MOF do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) prostředí nebo [v Azure](./automation-dsc-onboarding.md#enable-azure-vms) a vyžádat si konfigurace.

Pokud si chcete vyzkoušet ReverseDSC, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Další kroky

- Informace o prostředí PowerShell DSC najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Přečtěte si o prostředcích PowerShellu v [prostředcích DSC](/powershell/scripting/dsc/resources/resources).
- Podrobnosti o konfiguraci místních Configuration Manager najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig).
