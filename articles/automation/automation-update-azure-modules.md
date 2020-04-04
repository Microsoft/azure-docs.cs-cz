---
title: Aktualizace modulů Azure v Azure Automation
description: Tento článek popisuje, jak teď můžete aktualizovat běžné moduly Azure PowerShell u předpokladu, které ve výchozím nastavení v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 721bb3579fec10df88be471d67f68c0846aa9432
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632116"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Postup aktualizace modulů Azure PowerShellu ve službě Azure Automation

Chcete-li aktualizovat moduly Azure ve vašem účtu Automation, musíte použít [runbook modulů Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), který je k dispozici jako open source. Pokud chcete začít používat runbook **Update-AutomationAzureModulesForAccount** k aktualizaci modulů Azure, stáhněte si ho z [úložiště modulů Runbook update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na GitHubu. Potom jej můžete importovat do účtu Automation nebo jej spustit jako skript. Informace o importu sady Runbook v účtu Automation naleznete v [tématu Import sady Runbook](manage-runbooks.md#importing-a-runbook).

Nejběžnější moduly AzureRM PowerShell jsou k dispozici ve výchozím nastavení v každém účtu automatizace. Tým Azure pravidelně aktualizuje moduly Azure, proto chcete udržovat aktuální, budete chtít použít runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) k aktualizaci modulů ve vašich účtech Automation.

Vzhledem k tomu, že moduly jsou pravidelně aktualizovány skupinou produktů, může dojít ke změnám s přiloženými rutinami. Tato akce může negativně ovlivnit vaše sady Runbook v závislosti na typu změny, jako je například přejmenování parametru nebo zcela zastaralá rutina.

Abyste se vyhnuli ovlivnění runbooků a procesů, které automatizují, otestujte a ověřte, než budete pokračovat. Pokud nemáte vyhrazený účet automatizace určený pro tento účel, zvažte jeho vytvoření, abyste mohli otestovat mnoho různých scénářů během vývoje vašich runbooků. Toto testování by mělo zahrnovat iterativní změny, jako je aktualizace modulů prostředí PowerShell.

Pokud vyvíjíte skripty místně, doporučujeme mít stejné verze modulu místně, které máte ve svém účtu Automation při testování, abyste zajistili, že obdržíte stejné výsledky. Po ověření výsledků a použití všech požadovaných změn můžete změny přesunout do produkčního prostředí.

> [!NOTE]
> Nový účet automatizace nemusí obsahovat nejnovější moduly.

> [!NOTE]
> Nebude možné odstranit globální moduly - moduly, které automatizace poskytuje po vybalení z krabice.

## <a name="considerations"></a>Požadavky

Níže jsou uvedeny některé důležité informace, které je třeba vzít v úvahu při použití tohoto procesu k aktualizaci modulů Azure:

* Tato runbook podporuje aktualizace modulů **Azure** a **AzureRm** ve výchozím nastavení. Tato kniha runbook podporuje také aktualizaci modulů **Az.** Další informace o aktualizaci `Az` modulů s touto runbookem najděte do [runbooku Update Azure.](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) Existují další důležité faktory, které je třeba `Az` vzít v úvahu při používání modulů ve vašem účtu Automation, abyste se dozvěděli více, viz [Použití modulů Az ve vašem účtu Automation](az-modules.md).

* Před spuštěním tohoto runbooku se ujistěte, že váš účet Automation má vytvořené [přihlašovací údaje účtu Azure Run As.](manage-runas-account.md)

* Tento kód můžete použít jako běžný skript PowerShellu namísto runbooku: stačí se nejprve přihlásit do Azure pomocí příkazu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) a pak předat `-Login $false` skriptu.

* Chcete-li použít tento runbook na `AzureRmEnvironment` suverénní chabé mraky, použijte parametr předat správné prostředí runbook.  Přijatelné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**a **AzureUSGovernment**. Tyto hodnoty lze načíst `Get-AzureRmEnvironment | select Name`z použití . Pokud tomuto parametru nepředáte hodnotu, runbook bude ve výchozím nastavení nastaven na Azure public cloud **AzureCloud**

* Pokud chcete použít konkrétní verzi modulu Azure PowerShell namísto nejnovější dostupné v Galerii PowerShellu, předajte tyto verze volitelnému `ModuleVersionOverrides` parametru runbooku **Update-AutomationAzureModulesForAccount.** Příklady naleznete [aktualizace AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Moduly Azure PowerShellu, které nejsou `ModuleVersionOverrides` uvedeny v parametru, se aktualizují nejnovějšími verzemi modulů v Galerii PowerShellu. Pokud parametru `ModuleVersionOverrides` nic nepředáte, všechny moduly se aktualizují nejnovějšími verzemi modulů v galerii prostředí PowerShell. Toto chování je stejné jako **tlačítko Aktualizovat moduly Azure.**

## <a name="next-steps"></a>Další kroky

Další informace najdete v open source [modulech Update Azure runbook.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
