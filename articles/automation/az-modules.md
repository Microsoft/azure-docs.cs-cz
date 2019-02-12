---
title: Pomocí Az moduly ve službě Azure Automation
description: Tento článek obsahuje informace o používání modulů Az ve službě Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e9240949c589717303fe00205c5374b5e3a6a791
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008255"
---
# <a name="az-module-support-in-azure-automation"></a>Podpora modulu az ve službě Azure Automation

Azure automation podporuje možnost používat [modul Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašich sadách runbook. Modul Az se neimportuje automaticky v jakékoli nové nebo existující účty Automation. Tento článek popisuje, jak používat Az moduly s Azure Automation.

## <a name="considerations"></a>Požadavky

Existuje mnoho věcí, které vzít v úvahu při použití modulu Az ve službě Azure Automation. Runbooky a moduly je možné používat řešení ve vašem účtu Automation. Úpravy sady runbook nebo upgradem moduly může potenciálně způsobit problémy pomocí svých runbooků. Měli byste otestovat všechny sady runbook a řešení pečlivě samostatného účtu Automation před importováním nového `Az` moduly. Žádné změny modulů mohou negativně ovlivnit vyšší úrovně řešení, jako je Správa aktualizací a spuštění/zastavení virtuálních počítačů mimo špičku. Doporučuje se nezmění modulů a runbooků v účty služby Automation, které obsahují všechna řešení. Toto chování se neomezuje jen na Az moduly. Toto chování měli vzít v úvahu při zavádění všechny změny do vašeho účtu Automation.

Import `Az` modulu ve vašem účtu Automation neimportuje automaticky modulu v relaci Powershellu, sady runbook použít. Moduly importují do relace prostředí PowerShell v následujících situacích:

* Při použití rutiny z modulu je vyvolána z runbooku
* Když runbook importuje ho explicitně pomocí `Import-Module` rutiny
* Když se jiný modul v závislosti na modulu naimportují do relace prostředí PowerShell

> [!IMPORTANT]
> Je důležité zajistit, aby této sady runbook v účtu Automation buď importovat pouze `Az` nebo `AzureRM` moduly do relace Powershellu používaných sad runbook a ne obojí. Pokud `Az` importována před `AzureRM` v sadě runbook, runbook dokončí, ale [chyba odkazuje na metodu get_SerializationSettings](/troubleshoot/runbooks.md#get-serializationsettings) se zobrazí v datové proudy úlohy a rutin nemusí být správně spustit. Pokud importujete `AzureRM` a potom `Az` runbooku, i nadále dokončení, ale zobrazí se chyba v datové proudy úlohy s informacemi o tom, které obě `Az` a `AzureRM` nelze importovat ve stejné relaci nebo použít ve stejném runbooku.

## <a name="migrating-to-az-modules"></a>Migrace na moduly Az

Doporučujeme, že testujete migraci tak, aby místo Az moduly AzureRM moduly v testu účtu Automation. Po vytvoření tohoto účtu Automation, můžete zajistit že hladký dostane vaši migraci do následující kroky:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Zastavit a odebrat z plánu všechny sady runbook, který používá moduly AzureRM

K zajištění spouštět existující sady runbook, který použít `AzureRM` rutiny, měli byste zastavit a odebrat z plánu všechny sady runbook, které používají `AzureRM` moduly. Uvidíte, jaké plány existovat a plány, které je třeba odebrat ručně spuštěním následujícího příkladu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité si každý plán zvlášť Ujistěte se, že vám mohou plánovanou zkoušku přeplánovat ho v budoucnosti pro vlastní runbooky v případě potřeby.

### <a name="import-the-az-modules"></a>Import modulů Az

Importujte pouze Az moduly, které jsou požadovány pro vlastní runbooky. Neimportujte kumulativní `Az` modul, protože obsahuje všechny `Az.*` moduly, které se mají naimportovat. Tento návod je stejný pro všechny moduly.

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modulu, představuje závislost pro ostatní `Az.*` moduly. Z tohoto důvodu tento modul musí být importován do vašeho účtu Automation, předtím, než importujete ostatní moduly.

Ve svém účtu Automation vyberte **moduly** pod **sdílené prostředky**. Klikněte na tlačítko **procházet galerii** otevřít **procházet galerii** stránky.  Do vyhledávacího pole zadejte název modulu (například `Az.Accounts`). Na stránce modul prostředí PowerShell, klikněte na tlačítko **importovat** k importu modulu do vašeho účtu Automation.

![Import modulů z účtu služby Automation](media/az-modules/import-module.png)

Tento proces importu je možné provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com) tak, že modul. Jakmile najdete v modulu, vyberte ho a v části **Azure Automation** klikněte na tlačítko **nasadit do Azure Automation**.

![Import modulů přímo z Galerie](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testovat vaše sady runbook

Jakmile `Az` moduly importují ve vašem účtu Automation, můžete teď začít upravovat runbooky místo toho použít Az modulu. Většina rutin mají stejný název s výjimkou `AzureRM` byl změněn na `Az`. Seznam modulů, které nepostupujte podle tohoto procesu najdete v tématu [seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names).

Jeden ze způsobů, jak testovat vaše sady runbook před úpravy sady runbook a používají nové rutiny, je použití `Enable-AzureRMAlias -Scope Process` na začátku sady runbook. Přidáním tohoto runbooku můžete vaše sada runbook poběží beze změn.

## <a name="after-migration-details"></a>Po migraci podrobnosti

Po dokončení migrace nezačínají sady runbook pomocí `AzureRM` moduly v účtu už. Doporučuje se také nemusíte importovat nebo aktualizovat `AzureRM` moduly na tento účet. Od tohoto okamžiku vezměte v úvahu tento účet migrován a `Az`a pracovat s `Az` pouze moduly. Nový účet Automation je vytvoření existující `AzureRM` zůstanou nainstalovány modulů a runbooků pro tento kurz bude pořád možné čtení zleva doprava `AzureRM` rutiny. Tyto sady runbook by neměl být spustili.

## <a name="next-steps"></a>Další postup

Další informace o používání modulů Az najdete v tématu [Začínáme s modulem Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).