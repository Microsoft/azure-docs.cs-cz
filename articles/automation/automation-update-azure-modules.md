---
title: Aktualizovat moduly Azure ve službě Azure Automation
description: Tento článek popisuje, jak můžete nyní aktualizovat běžných modulů Azure Powershellu, které jsou k dispozici ve výchozím nastavení ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a2f55891ddd383ea15da499495909b56ffb0e06d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786146"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Aktualizace modulů Azure Powershellu ve službě Azure Automation

Chcete-li aktualizovat moduly Azure ve vašem účtu Automation, musíte použít [runbooku Azure aktualizovat moduly](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), které je dostupné jako open source. Pokud chcete začít používat **aktualizace AutomationAzureModulesForAccount** stažení sady runbook, chcete-li aktualizovat moduly Azure z [Azure aktualizovat moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na Githubu. Potom můžete importovat do účtu služby Automation nebo spustit jako skript. Pokyny o tom, jak to udělat najdete v [aktualizace Azure moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

Nejčastější moduly AzureRM Powershellu jsou dostupné ve výchozím nastavení v každém účtu Automation. Tým Azure pravidelně aktualizuje moduly Azure, proto k zajištění aktuálnosti budete chtít použít [aktualizace AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sady runbook a aktualizujte moduly v rámci účtů služby Automation.

Protože moduly jsou pravidelně aktualizovány produktovou skupinou, změny můžou nastat pomocí rutiny zahrnuté. Tato akce může mít negativní vliv na vaše sady runbook v závislosti na typ změny, jako například přejmenování parametru nebo zcela vyřazení rutiny.

Aby se zabránilo dopadu na vaše sady runbook a procesy, automatizovat, testování a ověřování než budete pokračovat. Pokud nemáte určené pro tento účel vyhrazený účet služby Automation, zvažte vytvoření jednoho tak, aby můžete vyzkoušet řadu různých scénářů při vývoji vašich sadách runbook. Tento test by měl obsahovat iterativní změny, jako je aktualizace modulů prostředí PowerShell.

Pokud vyvíjíte skripty místně, doporučuje se mít stejnou verzí modulů místně, které máte ve svém účtu Automation při testování pro zajištění vám zobrazí stejné výsledky. Po výsledky se ověří a použili potřebné změny, můžete přesunout změny do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

## <a name="considerations"></a>Požadavky

Tady jsou některé aspekty, vezměte v úvahu při použití tohoto procesu se aktualizovat moduly Azure:

* Tato sada runbook podporuje, jenom aktualizace **Azure** a **AzureRm** aktuálně moduly. [Moduly Azure Powershellu Az](/powershell/azure/new-azureps-module-az) jsou podporované v účtech Automation, ale nelze aktualizovat pomocí tohoto runbooku. Existují důležité faktory, které je potřeba vzít v úvahu při použití `Az` modulů ve vašem účtu Automation, další informace najdete v článku [Az použití modulů ve vašem účtu Automation](az-modules.md).

* Vyhněte se spouští tento runbook na účty Automation, které obsahují Az moduly.

* Před spuštěním této sady runbook, ujistěte se, že má váš účet služby Automation [pověření účtu spustit jako pro Azure](manage-runas-account.md) vytvořili.

* Tento kód můžete použít jako regulární skript Powershellu místo sady runbook: stačí se přihlásit k Azure pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) první příkaz a pak předejte `-Login $false` do skriptu.

* Chcete-li použít tuto sadu runbook v suverénních cloudech, použijte `AzureRmEnvironment` parametr předat správné prostředí do runbooku.  Přípustné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, a **AzureUSGovernment**. Tyto hodnoty lze získat pomocí `Get-AzureRmEnvironment | select Name`. Pokud nemáte předat hodnotu tomuto parametru, sada runbook bude ve výchozím nastavení veřejného cloudu Azure **AzureCloud**

* Pokud chcete použít konkrétní verzi modulu Azure Powershellu místo nejnovější dostupné v galerii prostředí PowerShell, předat tyto verze volitelného `ModuleVersionOverrides` parametr **Update-AutomationAzureModulesForAccount**sady runbook. Příklady najdete v tématu [aktualizace AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) sady runbook. Moduly prostředí Azure PowerShell, které nejsou uvedené v `ModuleVersionOverrides` parametru jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Pokud předáte nic `ModuleVersionOverrides` parametr, všechny moduly jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Toto chování je stejné jako **aktualizace modulů Azure** tlačítko.

## <a name="known-issues"></a>Známé problémy

Existuje známý problém s aktualizací moduly AzureRM účtu Automation, který je ve skupině prostředků s názvem číselné, který začíná číslem 0. Pokud chcete aktualizovat moduly Azure ve vašem účtu Automation, musí být ve skupině prostředků s názvem alfanumerické znaky. Skupiny prostředků s číselnou názvy počínaje 0 se nám aktualizovat moduly AzureRM v tuto chvíli.

## <a name="next-steps"></a>Další postup

Navštivte open source [runbooku moduly Azure aktualizace](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Další informace o něm.
