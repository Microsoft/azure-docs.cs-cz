---
title: Podpora modulů Az ve službě Azure Automation
description: Tento článek obsahuje informace o používání modulů Az v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 21fa1c4faa4a080b9b495e1481fdadcd7e8bea10
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619477"
---
# <a name="az-module-support-in-azure-automation"></a>Podpora modulů Az ve službě Azure Automation

Azure Automation podporuje použití [modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašich runbookech. Souhrnný modul Az není importován automaticky v žádných nových nebo existujících účtech automatizace. 

## <a name="considerations"></a>Požadavky

Existuje mnoho věcí, které je třeba vzít v úvahu při použití kumulativní modul Uz v Azure Automation. Sady Runbook a moduly lze používat řešení vyšší úrovně ve vašem účtu Automation. Úpravy runbooků nebo upgradovacích modulů mohou potenciálně způsobit problémy s vašimi runbooky. Před importem nových modulů Az byste měli pečlivě otestovat všechny sady Runbook a řešení v samostatném účtu Automation. Jakékoli změny modulů mohou negativně ovlivnit řešení [Start/Stop.](automation-solution-vm-management.md) Nedoporučujeme měnit moduly a runbooky v účtech Automatizace, které obsahují žádná řešení. Toto chování není specifické pro moduly Az. Je třeba vzít v úvahu při zavádění jakékoli změny v účtu automatizace.

Import modulu Az v účtu Automation automaticky neimportuje modul v relaci prostředí PowerShell, který používají sady Runbook. Moduly se importují do relace prostředí PowerShell v následujících situacích:

* Když runbook vyvolá rutinu z modulu
* Když runbook importuje modul `Import-Module` explicitně s rutinou
* Když runbook importuje jiný modul v závislosti na modulu

> [!IMPORTANT]
> Ujistěte se, že runbooky v účtu Automation importují buď moduly Az nebo moduly [AzureRM,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) ale ne obojí, do relace Prostředí PowerShell. Pokud sada Runbook importuje moduly Az před moduly AzureRM, sada runbook se dokončí. Chyba odkazující na [rutinu Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) se však zobrazí v datových proudech úloh a rutinách úloh nemusí být správně spuštěna. Pokud sada Runbook importuje moduly AzureRM před moduly Az, sada Runbook se také dokončí. V tomto případě se však zobrazí chyba v datových proudech úloh oznamující, že az a AzureRM nelze importovat ve stejné relaci nebo použít ve stejném runbooku.

## <a name="migrating-to-az-modules"></a>Migrace na moduly Az

Doporučujeme otestovat migraci na moduly Az v testovacím účtu Automatizace. Po vytvoření tohoto účtu můžete pomocí pokynů v této části pracovat s moduly.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zastavení a zrušení plánování všech runbooků, které používají moduly AzureRM

Chcete-li zajistit, že nespustíte žádné existující sady Runbook, které používají moduly AzureRM, zastavte a odplánujte všechny ovlivněné sady Runbook. Můžete vidět, jaké plány existují a které plány odebrat spuštěním kódu podobného tomuto příkladu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité zkontrolovat každý plán zvlášť, abyste se ujistili, že ho můžete v budoucnu přeplánovat pro vaše sady Runbook, pokud je to nutné.

### <a name="import-the-az-modules"></a>Import modulů Az

>[!NOTE]
>Nechat své runbooky importovat pouze požadované moduly Az. Neimportujte souhrnný modul Az, protože obsahuje všechny moduly Az. Tento návod je stejný pro všechny moduly.

Modul [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro ostatní moduly Az. Z tohoto důvodu musí sady Runbook importovat tento modul do účtu Automation před importem jiných modulů.

Import modulů na webu Azure Portal:

1. V části **Sdílené prostředky**vyberte v části Sdílené prostředky **položku Moduly** . 
2. Kliknutím na **Procházet galerii** otevřete stránku Galerie procházení.  
3. Na vyhledávacím panelu zadejte název `Az.Accounts`modulu, například . 
4. Na stránce Modul prostředí PowerShell klikněte na **Importovat** modul do účtu Automation.

![Import modulů z účtu Automation](media/az-modules/import-module.png)

Tento proces importu lze provést také prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com) vyhledáním modulu pro import. Jakmile modul najdete, vyberte ho, zvolte kartu **Azure Automation** a klikněte na **Nasadit do Azure Automation**.

![Import modulů přímo z galerie](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testování runbooků

Po importu modulů Az do účtu Automation můžete začít upravovat runbooky tak, aby používaly moduly. Většina rutin má stejné názvy jako pro modul AzureRM, s tím rozdílem, že předpona AzureRM (nebo AzureRm) byla změněna na Az. Seznam modulů, které nedodržují tuto konvenci pojmenování, naleznete v [seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jedním ze způsobů, jak otestovat změnu runbooku pro `Enable-AzureRmAlias -Scope Process` použití nových rutin, je použití na začátku runbooku. Přidáním tohoto příkazu do runbooku může být skript spuštěn beze změn.

## <a name="after-migration-details"></a>Podrobnosti o migraci

Po dokončení migrace se nepokoušejte spustit runbooky pomocí modulů AzureRM na účtu Automation. Doporučujeme také neimportovat nebo aktualizovat moduly AzureRM na účtu. Zvažte účet migrovaný do Az a pracujte pouze s moduly Az. 

Když vytvoříte nový účet Automation, existující moduly AzureRM jsou stále nainstalované. Stále můžete aktualizovat výukové runbooky s rutinami AzureRM. Tyto sady Runbook byste však neměli spouštět.

## <a name="next-steps"></a>Další kroky

Další informace o používání modulů Az najdete [v tématu Začínáme s modulem Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
