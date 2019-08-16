---
title: Převést konfigurace na složené prostředky pro konfiguraci stavu – Azure Automation
description: Přečtěte si, jak převést konfigurace na složené prostředky pro konfiguraci stavu v Azure Automation.
keywords: DSC, PowerShell, konfigurace, instalace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9e76532d41e23cba376755ca524ca6911385204
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559526"
---
# <a name="convert-configurations-to-composite-resources"></a>Převod konfigurací na složené prostředky

> Platí pro: Windows PowerShell 5.1

Jakmile začnete s konfiguracemi, můžete rychle vytvořit "scénáře", které spravují skupiny nastavení.
Příkladem může být:

- Vytvoření webového serveru
- Vytvoření serveru DNS
- Vytvoření serveru SharePoint
- Konfigurace clusteru SQL
- Správa nastavení brány firewall
- spravovat nastavení hesla

Pokud vás zajímá sdílení této práce s ostatními, je nejlepší volbou pro zabalení konfigurace jako [složeného prostředku](/powershell/dsc/resources/authoringresourcecomposite).
První vytvoření složených prostředků může být zahlcené.

> [!NOTE]
> Tento článek popisuje řešení, které spravuje komunita open source.
> Podpora je dostupná jenom ve formě spolupráce na GitHubu, ne od Microsoftu.

## <a name="community-project-compositeresource"></a>Komunitní projekt: CompositeResource

Pro vyřešení této výzvy bylo vytvořeno řešení udržované komunitou s názvem [CompositeResource](https://github.com/microsoft/compositeresource) .

CompositeResource automatizuje proces vytváření nového modulu z konfigurace.
Začnete tím [](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) , že nakonfigurujete konfigurační skript na pracovní stanici (nebo na serveru sestavení), aby byl načten do paměti.
Dále místo spuštění konfigurace pro vygenerování souboru MOF použijte funkci poskytnutou modulem CompositeResource k automatizaci převodu.
Rutina načte obsah vaší konfigurace, získá seznam parametrů a vygeneruje nový modul se všemi potřebnými.

Jakmile vygenerujete modul, můžete zvýšit verzi a přidat poznámky k verzi pokaždé, když provedete změny a publikujete ji do vlastního [úložiště PowerShellGet](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Jakmile vytvoříte složený modul prostředků obsahující vaši konfiguraci (nebo několik konfigurací), můžete je použít v [prostředí pro vytváření obsahu](/azure/automation/compose-configurationwithcompositeresources) v Azure nebo je přidat do [konfiguračních skriptů DSC](/powershell/dsc/resources/configurations) pro generování souborů MOF a [nahrajte soubory MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Pak můžete své servery zaregistrovat z [místního](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) prostředí nebo [v Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) a vyžádat si konfigurace.
Nejnovější aktualizace projektu také publikovala [Runbooky](https://www.powershellgallery.com/packages?q=DscGallerySamples) pro Azure Automation pro automatizaci procesu importu konfigurací z Galerie prostředí PowerShell.

Chcete-li se pokusit automatizovat vytváření složených prostředků pro DSC, navštivte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/compositeresource/) a Stáhněte řešení nebo klikněte na web projektu a zobrazte [dokumentaci](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Další postup

- [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/dsc/overview/overview)
- [Prostředky DSC](/powershell/dsc/resources/resources)
- [Konfigurace místní Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
