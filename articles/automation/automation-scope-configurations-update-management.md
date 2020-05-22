---
title: Práce s konfiguracemi oboru pro Azure Automation Update Management
description: Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95563e816fa4f0931e547b6bd163dce23c338a44
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749120"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Práce s konfiguracemi oboru pro Update Management

Tento článek popisuje, jak můžete pracovat s konfiguracemi oboru při použití funkce [Update Management](automation-update-management.md) na virtuálních počítačích. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Ověřit konfiguraci oboru

Update Management používá konfiguraci oboru v rámci pracovního prostoru Log Analytics pro cílení na počítače, které mají být pro funkci povoleny. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu funkce na konkrétní počítače. Přístup k konfiguracím oboru:

1. V účtu Automation v části **související prostředky**vyberte **pracovní prostor**. 

2. Zvolte pracovní prostor v části **zdroje dat pracovního prostoru**a vyberte **Konfigurace oboru**.

3. Pokud ve vybraném pracovním prostoru není ještě povolená funkce Update Management, vytvoří se `MicrosoftDefaultScopeConfig-Updates` Konfigurace oboru. 

4. Pokud má vybraný pracovní prostor již funkci povolenou, nebude znovu nasazena a do ní není přidána konfigurace oboru. 

5. V libovolné konfiguraci oboru vyberte tři tečky a pak klikněte na **Upravit**. 

6. V podokně úpravy vyberte **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru.

## <a name="view-a-saved-search"></a>Zobrazení uloženého hledání

Když se do Update Management přidá počítač, přidá se taky do uloženého hledání ve vašem pracovním prostoru. Uložené výsledky hledání je dotaz, který obsahuje cílové počítače.

1. Přejděte do pracovního prostoru Log Analytics a v části **Obecné**vyberte **uložená hledání** . Uložené hledání, které používá Update Management, je:

|Name     |Kategorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

2. Výběrem uloženého hledání Zobrazte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    ![Uložená hledání](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Další kroky

* Obecné informace o této funkci najdete v tématu [přehled Update Management](automation-update-management.md).
* Informace o tom, jak používat tuto funkci, najdete v tématu [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Pokud chcete funkci povolit pomocí účtu Automation, přečtěte si téma [povolení Update Management z účtu Automation](automation-onboard-solutions-from-automation-account.md).
* Pokud chcete tuto funkci povolit, přejděte na Azure Portal [povolení Update Management z Azure Portal](automation-onboard-solutions-from-browse.md).
* Chcete-li použít sadu Runbook k povolení této funkce, přečtěte si téma [povolení Update Management ze sady Runbook](automation-onboard-solutions.md).
* Pokud chcete funkci povolit pomocí virtuálního počítače Azure, přečtěte si téma [povolení Update Management z virtuálního počítače Azure](automation-onboard-solutions-from-vm.md).
* Informace o řešení chyb funkcí najdete v tématu [řešení potíží s Update Management](troubleshoot/update-management.md).
* Problémy s chybami agenta Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Problémy s chybami agenta aktualizací pro Linux najdete v tématu [řešení potíží s aktualizacemi agenta pro Linux](troubleshoot/update-agent-issues-linux.md).