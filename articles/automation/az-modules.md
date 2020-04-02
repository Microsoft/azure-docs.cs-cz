---
title: Podpora modulů Az ve službě Azure Automation
description: Tento článek obsahuje informace o používání modulů Az v Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548349"
---
# <a name="az-module-support-in-azure-automation"></a>Podpora modulů Az ve službě Azure Automation

Azure Automation podporuje použití [modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašich runbookech. Modul Az se neimportuje automaticky v žádných nových nebo existujících účtech automatizace. 

## <a name="considerations"></a>Požadavky

Při použití modulu Az v Azure Automation je třeba vzít v úvahu mnoho věcí. Sady Runbook a moduly lze používat řešení vyšší úrovně ve vašem účtu Automation. Úpravy runbooků nebo upgradovacích modulů mohou potenciálně způsobit problémy s vašimi runbooky. Před importem nových `Az` modulů byste měli pečlivě otestovat všechny sady Runbook a řešení v samostatném účtu Automatizace. Jakékoli změny modulů mohou negativně ovlivnit řešení [Start/Stop.](automation-solution-vm-management.md) Nedoporučujeme měnit moduly a runbooky v účtech Automatizace, které obsahují žádná řešení. Toto chování není specifické pro moduly Az. Toto chování je třeba vzít v úvahu při zavádění jakékoli změny účtu automatizace.

Import modulu `Az` v účtu Automation automaticky neimportuje modul v relaci prostředí PowerShell, kterou sady Runbook používají. Moduly se importují do relace prostředí PowerShell v následujících situacích:

* Když je rutina z modulu vyvolána z runbooku
* Když jej runbook importuje `Import-Module` explicitně pomocí rutiny
* Při importu jiného modulu v závislosti na modulu do relace prostředí PowerShell

> [!IMPORTANT]
> Je důležité se ujistit, že sady Runbook `Az` `AzureRM` v účtu automatizace importovat nebo moduly do relací prostředí PowerShell používá runbooka a ne obojí. Pokud `Az` je importován před `AzureRM` v runbooku, sada Runbook dokončí, ale chyba odkazující na [rutinu Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) se zobrazí v datových proudech úloh a rutiny nemusí být správně provedeny. Pokud `AzureRM` importujete a `Az`potom , sada Runbook se stále dokončí, ale `Az` `AzureRM` v datových proudech úloh se zobrazí chyba, která uvádí, že oba a nelze je importovat ve stejné relaci nebo použít ve stejném runbooku.

## <a name="migrating-to-az-modules"></a>Migrace na moduly Az

Doporučujeme otestovat migraci na moduly Az v testovacím účtu Automatizace. Po vytvoření účtu automatizace můžete pomocí pokynů v této části pracovat s moduly.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Zastavení a zrušení plánování všech runbooků, které používají rutiny AzureRM

Chcete-li zajistit, že nespustíte `AzureRM` žádné existující sady Runbook, které používají rutiny, měli byste zastavit a zrušit plánování všech runbooků, které používají `AzureRM` moduly. Můžete vidět, jaké plány existují a které plány musí být odebrány spuštěním kódu podobného tomuto příkladu.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité zkontrolovat každý plán zvlášť, abyste se ujistili, že ho můžete v budoucnu přeplánovat pro vaše sady Runbook, pokud je to nutné.

### <a name="import-the-az-modules"></a>Import modulů Az

Importujte pouze moduly Az, které jsou vyžadovány pro vaše sady Runbook. Neimportujte souhrnný `Az` modul, protože obsahuje `Az.*` všechny moduly. Tento návod je stejný pro všechny moduly.

Modul [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro `Az.*` ostatní moduly. Z tohoto důvodu je třeba tento modul importovat do účtu automatizace před importem jiných modulů.

V části **Sdílené prostředky**vyberte v části Sdílené prostředky **položku Moduly** . Kliknutím na **Procházet galerii** otevřete stránku **Galerie procházení.**  Na vyhledávacím panelu zadejte název `Az.Accounts`modulu (například). Na stránce Modul prostředí PowerShell klikněte na **Importovat** modul do účtu Automation.

![Import modulů z účtu Automation](media/az-modules/import-module.png)

Tento proces importu lze provést také prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com) vyhledáním modulu pro import. Jakmile modul najdete, vyberte ho a na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

![Import modulů přímo z galerie](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testování runbooků

Jakmile `Az` jsou moduly importovány do vašeho účtu Automation, můžete začít upravovat sady Runbook a používat moduly Az. Většina rutin má stejné názvy s výjimkou, že `AzureRM` `Az`byla změněna na . Seznam modulů, které nedodržují tuto konvenci pojmenování, naleznete v [seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jedním ze způsobů, jak otestovat změnu runbooku pro `Enable-AzureRMAlias -Scope Process` použití nových rutin, je použití na začátku runbooku. Přidáním tohoto příkazu do runbooku může být skript spuštěn beze změn.

## <a name="after-migration-details"></a>Podrobnosti o migraci

Po dokončení migrace se již nepokoušejte spustit `AzureRM` sady Runbook pomocí modulů na účtu Automation. Doporučujese také neimportovat nebo `AzureRM` aktualizovat moduly v účtu. Zvažte účet migrovaný `Az`na `Az` aplikaci a pracujte pouze s moduly. 

Při vytvoření nového účtu automatizace jsou existující `AzureRM` moduly stále nainstalovány. Stále můžete aktualizovat runbooky `AzureRM` kurzu s rutinami. Tyto runbooky byste neměli spouštět.

## <a name="next-steps"></a>Další kroky

Další informace o používání modulů Az najdete [v tématu Začínáme s modulem Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
