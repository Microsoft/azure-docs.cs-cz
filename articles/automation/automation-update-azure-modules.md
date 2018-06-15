---
title: Aktualizovat Azure modulů ve službě Azure Automation
description: Tento článek popisuje, jak můžete nyní aktualizovat společnými moduly prostředí Azure PowerShell, které jsou dostupné ve výchozím nastavení ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fe263346d8794b3dc85b6420d8b9b02efa5f9684
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193501"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Postup aktualizace modulů prostředí Azure PowerShell ve službě Azure Automation

Nejběžnější modulů prostředí Azure PowerShell jsou dostupné ve výchozím nastavení v jednotlivých účtů Automation. Tým Azure Azure moduly aktualizuje pravidelně, tak v účtu Automation jsou k dispozici způsob, jak aktualizovat moduly v účtu, pokud je nová verze dostupná z portálu.  

Protože moduly se pravidelně aktualizují v product group, změny můžou nastat pomocí rutiny zahrnuté, což může mít negativní vliv na vaše sady runbook v závislosti na typu změnu, jako je například přejmenování parametr nebo zcela místo začne rutiny. Abyste se vyhnuli, runbooků a procesy, které budou automatizovat, které mají vliv, se doporučuje, testování a než budete pokračovat, ověřte. Pokud nemáte určené pro tento účel vyhrazený účet Automation, zvažte vytvoření jednoho tak, aby při vývoji vaší sady runbook, kromě iterativní změny jako aktualizace moduly Powershellu můžete otestovat mnoha různých scénářů a permutací. Po ověření výsledky a jste použili potřebné změny, pokračovat v migraci žádné sady runbook, který vyžaduje úpravu koordinace a provést následující aktualizace, jak je popsáno v produkčním prostředí.

## <a name="updating-azure-modules"></a>Aktualizace Azure moduly

1. Na stránce modulů účtu Automation je možnost **moduly Azure aktualizace**. Je vždy povolena.<br><br> ![Aktualizovat moduly Azure možnost stránce modulů](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klikněte na tlačítko **moduly Azure aktualizace**, se zobrazí oznámení o potvrzení, která požádá, pokud chcete pokračovat.<br><br> ![Moduly Azure oznámení o aktualizaci](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klikněte na tlačítko **Ano** a zahájí se proces aktualizace modulu. Proces aktualizace trvá asi 15-20 minut aktualizovat následující moduly:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Pokud moduly jsou již aktuální, proces dokončení za několik sekund. Po dokončení procesu aktualizace, budete upozorněni.<br><br> ![Aktualizovat stav aktualizace moduly Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Při spuštění novou naplánovanou úlohu automatizace Azure používá nejnovější modulů ve vašem účtu Automation.    

Pokud používáte rutiny z těchto modulů prostředí Azure PowerShell ve vašich sadách runbook, chcete tento proces aktualizace spustit každý měsíc nebo, ujistěte se, že máte nejnovější moduly.

## <a name="next-steps"></a>Další postup

* Další informace o integrační moduly a jak vytvořit vlastní moduly k další integraci automatizace s jinými systémy, služby nebo řešení, najdete v části [moduly integrace](automation-integration-modules.md).

* Zvažte použití integrace zdroj ovládacího prvku [Githubu Enterprise](automation-scenario-source-control-integration-with-github-ent.md) nebo [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) centrálně spravovat a řídit verzích portfolio automatizace sady runbook a konfigurace.  