---
title: Převod konfigurací na složené prostředky pro konfiguraci stavu – Azure Automation
description: Zjistěte, jak převést konfigurace na složené prostředky pro konfiguraci stavu v Azure Automation.
keywords: dsc,powershell,konfigurace,nastavení
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585514"
---
# <a name="convert-configurations-to-composite-resources"></a>Převod konfigurací na složené prostředky

> Platí pro: Windows PowerShell 5.1

Jakmile začnete vytvářet konfigurace, můžete rychle vytvořit "scénáře", které spravují skupiny nastavení.
Příkladem může být:

- vytvoření webového serveru
- vytvoření serveru DNS
- vytvoření sharepointového serveru
- konfigurace clusteru SQL
- správa nastavení brány firewall
- správa nastavení hesla

Máte-li zájem o sdílení této práce s ostatními, nejlepší možností je zabalit konfiguraci jako [složený prostředek](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Vytvoření složených prostředků poprvé může být ohromující.

> [!NOTE]
> Tento článek odkazuje na řešení, které je udržováno komunitou open source.
> Podpora je k dispozici pouze ve formě spolupráce githubu, ne od Microsoftu.

## <a name="community-project-compositeresource"></a>Komunitní projekt: CompositeResource

Komunita udržované řešení s názvem [CompositeResource](https://github.com/microsoft/compositeresource) byla vytvořena k vyřešení tohoto problému.

CompositeResource automatizuje proces vytváření nového modulu z vaší konfigurace.
Můžete začít [dot získávání](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) konfigurační skript na pracovní stanici (nebo sestavení serveru), takže je načten do paměti.
Dále, spíše než spuštění konfigurace pro generování souboru MOF, použijte funkci poskytovanou modulem CompositeResource k automatizaci převodu.
Rutina načte obsah vaší konfigurace, získá seznam parametrů a vygeneruje nový modul se vším, co potřebujete.

Jakmile vygenerujete modul, můžete zvýšit verzi a přidat poznámky k verzi pokaždé, když provedete změny a publikujete je do vlastního [úložiště PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Jakmile vytvoříte modul složených prostředků obsahující konfiguraci (nebo více konfigurací), můžete je použít v [prostředí composable Authoring Experience](/azure/automation/compose-configurationwithcompositeresources) v Azure nebo je přidat do [skriptů Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations) pro generování souborů MOF a [nahrání souborů MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak zaregistrujte své servery z [místního](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) pro vyžádat konfigurace.
Nejnovější aktualizace projektu také [publikovala runbooky](https://www.powershellgallery.com/packages?q=DscGallerySamples) pro Azure Automation pro automatizaci procesu importu konfigurací z Galerie Prostředí PowerShell.

Chcete-li vyzkoušet automatizaci vytváření složených zdrojů pro DSC, navštivte [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/compositeresource/) a stáhněte si řešení nebo klikněte na "Web projektu" a prohlédněte si [dokumentaci](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Další kroky

- [Windows PowerShell – přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview)
- [Zdroje dsc](/powershell/scripting/dsc/resources/resources)
- [Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaconfig)
