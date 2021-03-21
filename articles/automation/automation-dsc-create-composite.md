---
title: Převést konfigurace na složené prostředky pro konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak převést konfigurace na složené prostředky pro konfiguraci stavu Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563568"
---
# <a name="convert-configurations-to-composite-resources"></a>Převod konfigurací na složené prostředky

> Platí pro: Windows PowerShell 5,1

Jakmile začnete s konfiguracemi, můžete rychle vytvořit "scénáře", které spravují skupiny nastavení.
Příkladem může být:

- Vytvoření webového serveru
- Vytvoření serveru DNS
- Vytvoření serveru SharePoint
- Konfigurace clusteru SQL
- Správa nastavení brány firewall
- spravovat nastavení hesla

Pokud vás zajímá sdílení této práce s ostatními, je nejlepší volbou pro zabalení konfigurace jako [složeného prostředku](/powershell/scripting/dsc/resources/authoringresourcecomposite).
První vytvoření složených prostředků může být zahlcené.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-compositeresource"></a>Projekt komunity: CompositeResource

Pro vyřešení této výzvy bylo vytvořeno řešení udržované komunitou s názvem [CompositeResource](https://github.com/microsoft/compositeresource) .

CompositeResource automatizuje proces vytváření nového modulu z konfigurace.
Začnete tím, že nakonfigurujete konfigurační [skript na pracovní](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) stanici (nebo na serveru sestavení), aby byl načten do paměti.
Dále místo spuštění konfigurace pro vygenerování souboru MOF použijte funkci poskytnutou modulem CompositeResource k automatizaci převodu.
Rutina načte obsah vaší konfigurace, získá seznam parametrů a vygeneruje nový modul se všemi potřebnými.

Jakmile vygenerujete modul, můžete zvýšit verzi a přidat poznámky k verzi pokaždé, když provedete změny a publikujete ji do vlastního [úložiště PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Jakmile vytvoříte složený modul prostředků obsahující vaši konfiguraci (nebo několik konfigurací), můžete je použít v [prostředí pro vytváření obsahu](./compose-configurationwithcompositeresources.md) v Azure, nebo je přidat do [konfiguračních skriptů DSC](/powershell/scripting/dsc/configurations/configurations) pro generování souborů MOF a [nahrání souborů MOF do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) prostředí nebo [v Azure](./automation-dsc-onboarding.md#enable-azure-vms) a vyžádat si konfigurace.
Nejnovější aktualizace projektu také publikovala [Runbooky](https://www.powershellgallery.com/packages?q=DscGallerySamples) pro Azure Automation pro automatizaci procesu importu konfigurací z Galerie prostředí PowerShell.

Chcete-li se pokusit automatizovat vytváření složených prostředků pro DSC, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/compositeresource/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Další kroky

- Informace o prostředí PowerShell DSC najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Přečtěte si o prostředcích PowerShellu v [prostředcích DSC](/powershell/scripting/dsc/resources/resources).
- Podrobnosti o konfiguraci místních Configuration Manager najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig).
