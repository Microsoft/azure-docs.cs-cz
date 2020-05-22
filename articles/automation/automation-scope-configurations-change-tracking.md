---
title: Práce s konfiguracemi oboru pro Azure Automation Change Tracking a inventář
description: Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití Change Tracking a inventáře.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c30bd8a3bb4fa1085e56dd93c66c016c3612e352
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749134"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Práce s konfiguracemi oboru pro Change Tracking a inventář

Tento článek popisuje, jak můžete pracovat s konfiguracemi oboru při povolování funkce [Update Management](automation-update-management.md) na virtuálních počítačích. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Ověřit konfiguraci oboru

Update Management používá konfiguraci oboru v rámci pracovního prostoru Log Analytics pro cílení na počítače, které mají povolit Update Management. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu funkce na konkrétní počítače. Přístup k konfiguracím oboru:

1. V účtu Automation v části **související prostředky**vyberte **pracovní prostor**. 

2. Zvolte pracovní prostor v části **zdroje dat pracovního prostoru**a vyberte **Konfigurace oboru**.

3. Pokud ve vybraném pracovním prostoru není ještě povolená funkce Update Management, vytvoří se `MicrosoftDefaultScopeConfig-ChangeTracking` Konfigurace oboru. 

4. Pokud má vybraný pracovní prostor již funkci povolenou, nebude znovu nasazena a do ní není přidána konfigurace oboru. 

5. V libovolné konfiguraci oboru vyberte tři tečky a pak klikněte na **Upravit**. 

6. V podokně úpravy vyberte **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru.

## <a name="view-a-saved-search"></a>Zobrazení uloženého hledání

Když se do Change Tracking a inventáře přidá počítač, přidá se taky do uloženého hledání ve vašem pracovním prostoru. Uložené výsledky hledání je dotaz, který obsahuje cílové počítače.

1. Přejděte do pracovního prostoru Log Analytics a v části **Obecné**vyberte **uložená hledání** . Uložené hledání, které používá Update Management, je:

    |Name     |Kategorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Výběrem uloženého hledání Zobrazte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    ![Uložená hledání](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Další kroky

* Obecné informace o této funkci najdete v tématu [přehled Change Tracking a inventáře](change-tracking.md).
* Informace o práci s touto funkcí najdete v tématu [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Pokud chcete funkci povolit pomocí účtu Automation, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](automation-enable-changes-from-auto-acct.md).
* Pokud chcete funkci povolit pomocí Azure Portal, přečtěte si téma [povolení Change Tracking a inventáře z Azure Portal](automation-enable-changes-from-browse.md).
* Chcete-li použít sadu Runbook k povolení této funkce, přečtěte si téma [povolení Change Tracking a inventáře ze sady Runbook](automation-enable-changes-from-runbook.md).
* Pokud chcete použít virtuální počítač Azure k povolení této funkce, přečtěte si téma [povolení Change Tracking a inventáře z virtuálního počítače Azure](automation-enable-changes-from-vm.md).
* Informace o řešení problémů se sledováním změn a inventáře na VIRTUÁLNÍm počítači najdete v tématu [řešení potíží se změnami na virtuálním počítači Azure](automation-tutorial-troubleshoot-changes.md).
* Řešení chyb funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).