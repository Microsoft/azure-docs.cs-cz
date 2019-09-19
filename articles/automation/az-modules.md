---
title: Použití AZ modules in Azure Automation
description: Tento článek poskytuje informace pomocí AZ modules in Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f81c0affb78d5944b8ba910cccfa0be655f1a6f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097935"
---
# <a name="az-module-support-in-azure-automation"></a>AZ modul Support in Azure Automation

Azure Automation podporuje možnost použít [Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašich sadách Runbook. Modul AZ není automaticky importován do žádného nového nebo stávajícího účtu Automation. Tento článek popisuje použití funkce AZ modules with Azure Automation.

## <a name="considerations"></a>Požadavky

Při použití modulu AZ Module v Azure Automation je třeba vzít v úvahu mnoho věcí. Runbooky a moduly můžou používat řešení vyšší úrovně ve vašem účtu Automation. Úpravy runbooků nebo upgradované moduly můžou potenciálně způsobovat problémy se sadami Runbook. Před importem nových `Az` modulů byste měli všechny Runbooky a řešení pečlivě otestovat v samostatném účtu Automation. Jakékoli úpravy modulů můžou negativně [začínat a zastavovat](automation-solution-vm-management.md) řešení. V účtech Automation, které obsahují žádná řešení, se nedoporučuje měnit moduly a runbooky. Toto chování není specifické pro funkce AZ Modules. Toto chování je potřeba vzít v úvahu při zavádění změn v účtu Automation.

`Az` Import modulu v účtu Automation neimportuje automaticky modul v relaci PowerShellu, kterou používají Runbooky. Moduly se importují do relace PowerShellu v následujících situacích:

* Při vyvolání rutiny z modulu z Runbooku
* Pokud je sada Runbook naimportovaná explicitně `Import-Module` pomocí rutiny
* Když se jiný modul v závislosti na modulu importuje do relace PowerShellu

> [!IMPORTANT]
> Je důležité zajistit, aby Runbooky v účtu Automation buď importovaly `Az` , nebo `AzureRM` moduly jenom do relací PowerShellu, které používají Runbooky, a ne obojí. Pokud `Az` se předá `AzureRM` v Runbooku, sada Runbook se dokončí, ale v datových proudech úlohy se zobrazí chyba, která se [odkazuje na metodu get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) , ale rutiny se nemusely správně spustit. Pokud importujete `AzureRM` a pak `Az` Runbook bude i nadále dokončený, zobrazí se v datových proudech úloh chyba s oznámením, že obojí `Az` a `AzureRM` nelze importovat do stejné relace nebo použít ve stejné sadě Runbook.

## <a name="migrating-to-az-modules"></a>Migrace na az modules

Doporučuje se test migrace na používání AZ moduls místo AzureRM modulů v účtu testovací automatizace. Po vytvoření účtu Automation můžete pomocí následujících kroků zajistit plynulé dokončení migrace:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zastavit a zrušit plán pro sadu Runbook, která používá moduly AzureRM

Abyste se ujistili, že nespouštíte žádné existující Runbooky `AzureRM` , které používají rutiny, měli byste zastavit a zrušit plán pro `AzureRM` všechny Runbooky, které používají moduly. Můžete zjistit, jaké plány existují a které plány je nutné odebrat spuštěním následujícího příkladu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité si projít každý plán zvlášť, abyste se ujistili, že v případě potřeby můžete v budoucnu změnit plán pro vaše Runbooky.

### <a name="import-the-az-modules"></a>Import modulů AZ

Importujte pouze moduly AZ, které jsou požadovány pro vaše Runbooky. Neimportujte souhrnný `Az` modul, protože zahrnuje všechny `Az.*` moduly, které mají být importovány. Tento návod je stejný pro všechny moduly.

Modul [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislostí pro ostatní `Az.*` moduly. Z tohoto důvodu je potřeba tento modul naimportovat do svého účtu Automation ještě před importem jakýchkoli jiných modulů.

Z účtu Automation v části **sdílené prostředky**vyberte **moduly** . Kliknutím na **Procházet galerii** otevřete stránku **Procházet galerii** .  Na panelu hledání zadejte název modulu (například `Az.Accounts`). Na stránce modul prostředí PowerShell klikněte na **importovat** a importujte modul do svého účtu Automation.

![Importovat moduly z účtu Automation](media/az-modules/import-module.png)

Tento proces importu je možné provést také pomocí [Galerie prostředí PowerShell](https://www.powershellgallery.com) hledáním modulu. Jakmile najdete modul, vyberte ho a na kartě **Azure Automation** klikněte na **nasadit a Azure Automation**.

![Import modulů přímo z Galerie](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testování runbooků

Až se `Az` moduly naimportují do svého účtu Automation, teď můžete začít upravovat své Runbooky a místo toho použít příkaz AZ Module. Většina rutin má stejný název, s výjimkou `AzureRM` , že byla změněna na. `Az` Seznam modulů, které tento proces nenásledují, najdete v tématu [seznam výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jedním ze způsobů, jak testovat Runbooky před změnou sady Runbook tak, aby používaly nové rutiny `Enable-AzureRMAlias -Scope Process` , je použití na začátku Runbooku. Přidáním tohoto prostředí do Runbooku můžete Runbook spustit beze změn.

## <a name="after-migration-details"></a>Podrobnosti o migraci

Po dokončení migrace nespouštějte Runbooky, které už nepoužívají `AzureRM` moduly na účtu. Také doporučujeme Neimportovat ani aktualizovat `AzureRM` moduly na tomto účtu. Od tohoto okamžiku zvažte, že se tento účet migruje do `Az`služby a funguje jenom s `Az` moduly. Když se vytvoří nový účet Automation, existující `AzureRM` moduly se pořád nainstalují a runbooky se budou dál vytvářet pomocí `AzureRM` rutin. Tyto Runbooky by se neměly spouštět.

## <a name="next-steps"></a>Další kroky

Další informace o použití AZ modules najdete v tématu [Začínáme s AZ Module](/powershell/azure/get-started-azureps?view=azps-1.1.0).
