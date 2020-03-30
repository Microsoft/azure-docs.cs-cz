---
title: Použití modulů Az ve službě Azure Automation
description: Tento článek obsahuje informace pomocí modulů Az v Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986100"
---
# <a name="az-module-support-in-azure-automation"></a>Podpora modulů Az ve službě Azure Automation

Azure automation podporuje možnost používat [modul Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašich runbookech. Modul Az se automaticky neimportuje do žádných nových nebo existujících účtů automatizace. Tento článek popisuje, jak používat moduly Az s Azure Automation.

## <a name="considerations"></a>Požadavky

Při použití modulu Az v Azure Automation je třeba vzít v úvahu mnoho věcí. Sady Runbook a moduly lze používat řešenívyšší úrovně ve vašem účtu Automation. Úpravy runbooků nebo upgradovacích modulů mohou potenciálně způsobit problémy s vašimi runbooky. Před importem nových `Az` modulů byste měli pečlivě otestovat všechny sady Runbook a řešení v samostatném účtu automatizace. Jakékoli změny modulů mohou negativně ovlivnit řešení [Start/Stop.](automation-solution-vm-management.md) Nedoporučujeme měnit moduly a runbooky v účtech automatizace, které obsahují žádná řešení. Toto chování není specifické pro moduly Az. Toto chování je třeba vzít v úvahu při zavádění jakékoli změny účtu automatizace.

Import modulu `Az` v účtu automation automaticky neimportuje modul v relaci prostředí PowerShell, kterou sady Runbook používají. Moduly se importují do relace prostředí PowerShell v následujících situacích:

* Když je rutina z modulu vyvolána z runbooku
* Když jej runbook importuje `Import-Module` explicitně pomocí rutiny
* Při importu jiného modulu v závislosti na modulu do relace prostředí PowerShell

> [!IMPORTANT]
> Je důležité se ujistit, že sady Runbook `Az` `AzureRM` v účtu automatizace importovat nebo moduly do relací prostředí PowerShell používá runbooka a ne obojí. Pokud `Az` je importován před `AzureRM` v runbooku, sada Runbook bude dokončena, ale chyba [odkazující na get_SerializationSettings metoda](troubleshoot/runbooks.md#get-serializationsettings) se zobrazí v datových proudech úloh a rutiny nemusí být správně provedeny. Pokud `AzureRM` importujete a `Az` potom bude sada Runbook stále dokončena, ale `Az` v `AzureRM` datových proudech úloh se zobrazí chyba, která uvádí, že oba a nelze je importovat ve stejné relaci nebo použít ve stejném runbooku.

## <a name="migrating-to-az-modules"></a>Migrace na moduly Az

Doporučujeme otestovat migraci na použití modulů Az namísto modulů AzureRM v testovacím účtu Automation. Po vytvoření účtu Automation můžete pomocí následujících kroků zajistit, aby migrace proběhla hladce:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zastavení a zrušení plánování všech runbooků, které používají moduly AzureRM

Chcete-li zajistit, že nespustíte `AzureRM` žádné existující sady Runbook, které používají rutiny, měli byste zastavit a zrušit plánování všech runbooků, které používají `AzureRM` moduly. Můžete zjistit, jaké plány existují a které plány musí být odebrány spuštěním následujícího příkladu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité zkontrolovat každý plán zvlášť, abyste se ujistili, že ho můžete v budoucnu přeplánovat pro vaše sady Runbook, pokud je to nutné.

### <a name="import-the-az-modules"></a>Import modulů Az

Importujte pouze moduly Az, které jsou vyžadovány pro vaše sady Runbook. Neimportujte souhrnný `Az` modul, protože obsahuje všechny `Az.*` moduly, které mají být importovány. Tento návod je stejný pro všechny moduly.

Modul [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro `Az.*` ostatní moduly. Z tohoto důvodu je třeba tento modul importovat do účtu automatizace před importem jiných modulů.

V části **Sdílené prostředky**vyberte v části Sdílené prostředky **položku Moduly** . Kliknutím na **Procházet galerii** otevřete stránku **Galerie procházení.**  Na vyhledávacím panelu zadejte název `Az.Accounts`modulu (například). Na stránce Modul prostředí PowerShell klikněte na **Importovat** modul do účtu Automation.

![Import modulů z automatizačního účtu](media/az-modules/import-module.png)

Tento proces importu lze provést také prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com) vyhledáním modulu. Jakmile modul najdete, vyberte ho a na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

![Import modulů přímo z galerie](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Otestujte své runbooky

Jakmile `Az` jsou moduly importovány do vašeho účtu Automation, můžete nyní začít upravovat sady Runbook a místo toho použít modul Az. Většina rutin má stejný název, s výjimkou `AzureRM` byla změněna na `Az`. Seznam modulů, které nesledují tento proces, naleznete [v seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jedním ze způsobů, jak otestovat sady Runbook před úpravou sady `Enable-AzureRMAlias -Scope Process` Runbook tak, aby používala nové rutiny, je použití na začátku sady Runbook. Přidáním tohoto do runbooku, runbook můžete spustit beze změn.

## <a name="after-migration-details"></a>Po migraci podrobnosti

Po dokončení migrace už nespouštějte `AzureRM` sady Runbook pomocí modulů na účtu. Doporučujeme také neimportovat ani `AzureRM` aktualizovat moduly v tomto účtu. Od tohoto okamžiku zvažte, zda `Az`je tento `Az` účet migrován na aplikaci a pracujte pouze s moduly. Při vytvoření nového účtu `AzureRM` automatizace existující moduly budou stále nainstalovány a `AzureRM` sady Runbook pro výukové programy budou stále vytvořeny s rutinami. Tyto sady runbooky by neměly být spuštěny.

## <a name="next-steps"></a>Další kroky

Další informace o používání modulů Az najdete [v tématu Začínáme s modulem Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
