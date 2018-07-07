---
title: Aktualizovat moduly Azure ve službě Azure Automation
description: Tento článek popisuje, jak můžete nyní aktualizovat běžných modulů Azure Powershellu, které jsou k dispozici ve výchozím nastavení ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f968bd75c7aee4ad390f9206bb1fa210e7fcd8ff
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903145"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Aktualizace modulů Azure Powershellu ve službě Azure Automation

Ve výchozím nastavení v jednotlivých účtů Automation jsou k dispozici většiny běžných modulů Azure Powershellu. Tým Azure moduly Azure aktualizuje pravidelně, tak účet služby Automation jsou k dispozici způsob, jak aktualizovat moduly v účtu, když je nová verze dostupná z portálu.

Protože moduly jsou pravidelně aktualizovány produktovou skupinou, změny můžou nastat s zahrnuté rutin, které může mít negativní vliv na vaše sady runbook v závislosti na typ změny, jako například přejmenování parametru nebo zcela vyřazení rutiny. Aby se zabránilo dopadu na vaše sady runbook a procesy, které jsou automatizovat, se doporučuje otestovat a ověřit před pokračováním. Pokud nemáte určené pro tento účel vyhrazený účet služby Automation, zvažte vytvoření jednoho tak, aby můžete otestovat mnoho různých scénářů a permutací při vývoji vaší sady runbook kromě iterativní změny, jako je aktualizace Moduly Powershellu. Po výsledcích ověření a použili jste potřebné změny, pokračujte v koordinaci migrace všechny runbooky, který vyžaduje úpravu a proveďte následující aktualizace, jak je popsáno v produkčním prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

## <a name="updating-azure-modules"></a>Aktualizace modulů Azure

1. Na stránce modulů vašeho účtu Automation je možnost **aktualizace modulů Azure**. Je vždy povoleno.<br><br> ![Aktualizace modulů Azure možnost na stránce modulů](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klikněte na tlačítko **aktualizace modulů Azure**, zobrazí potvrzení oznámení, která požádá, pokud chcete pokračovat.<br><br> ![Aktualizace modulů Azure oznámení](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klikněte na tlačítko **Ano** a zahájí se proces aktualizace modulu. Proces aktualizace trvá asi 15 až 20 minut a aktualizujte následující moduly:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Pokud moduly jsou již aktuální, proces dokončí za pár sekund. Po dokončení procesu aktualizace, budete upozorněni.<br><br> ![Aktualizovat stav aktualizace modulů Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Při spuštění novou naplánovanou úlohu Azure Automation používá nejnovější moduly ve vašem účtu Automation.    

Pokud používáte rutiny z těchto modulů Azure Powershellu ve vašich sadách runbook, chcete spustit tento proces aktualizace každý měsíc nebo proto ujistěte se, že máte nejnovější moduly.

## <a name="next-steps"></a>Další postup

* Další informace o integrační moduly a vytvoření vlastních modulů k další integraci s jinými systémy, služby nebo řešení automatizace, naleznete v tématu [moduly integrace](automation-integration-modules.md).

* Zvažte použití integrace zdrojového ovládacího prvku [Githubu Enterprise](automation-scenario-source-control-integration-with-github-ent.md) nebo [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) centrálně spravovat a řídit verzích portfolio automatizace sady runbook a konfigurace.  
