---
title: Zprovoznění řešení Update Management, Change Tracking a inventáře z virtuálního počítače Azure
description: Naučte se, jak připojit virtuální počítač Azure pomocí Update Management, Change Tracking a řešení inventáře, která jsou součástí Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537028"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Zprovoznění řešení Update Management, Change Tracking a inventáře z virtuálního počítače Azure

Azure Automation poskytuje řešení, která vám pomůžou spravovat aktualizace zabezpečení operačního systému, sledovat změny a inventář, který je na vašich počítačích nainstalovaný. Existuje několik způsobů, jak připojit počítače. Řešení můžete připojit z virtuálního počítače, [z účtu Automation](automation-onboard-solutions-from-automation-account.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md)nebo pomocí [Runbooku](automation-onboard-solutions.md). Tento článek popisuje připojování těchto řešení z virtuálního počítače Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-the-solutions"></a>Povolit řešení

Nejdřív na svém VIRTUÁLNÍm počítači povolte jedno nebo všechna tři řešení:

1. V [Azure Portal](https://portal.azure.com)vyberte **virtuální počítače** nebo vyhledejte a vyberte **virtuální počítače** z domovské stránky.
2. Vyberte virtuální počítač, pro který chcete řešení povolit.
3. Na stránce virtuální počítač v části **operace**vyberte **Správa aktualizací**, **inventarizace**nebo **sledování změn**. Virtuální počítač může existovat v jakékoli oblasti bez ohledu na umístění vašeho účtu Automation. Při připojování řešení z virtuálního počítače musíte mít `Microsoft.OperationalInsights/workspaces/read` oprávnění k určení, jestli se virtuální počítač připojil k pracovnímu prostoru. Další informace o dalších požadovaných oprávněních najdete v tématu [oprávnění potřebná k připojování počítačů](automation-role-based-access-control.md#onboarding-permissions). Informace o tom, jak připojit více počítačů najednou, najdete v článku připojení [řešení Update Management, Change Tracking a inventáře](automation-onboard-solutions-from-automation-account.md).

4. Vyberte pracovní prostor Azure Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

![Připojení Update Managementho řešení](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Přejít na další řešení a pak vyberte **Povolit**. Rozevírací seznamy Log Analytics pracovní prostor a účet Automation jsou zakázané, protože tato řešení používají stejný pracovní prostor a účet Automation jako dříve povolené řešení.

> [!NOTE]
> Změna sledování a inventarizace používá stejné řešení. Pokud je jedno z těchto řešení povoleno, je povolena také druhá.

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá v pracovním prostoru konfiguraci oboru pro cílení na počítače, které řešení obdrží. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu řešení na konkrétní počítače. Přístup k konfiguracím oboru:

1. V účtu Automation v části **související prostředky**vyberte **pracovní prostor**. 
2. V pracovním prostoru v části **zdroje dat pracovního prostoru**vyberte **Konfigurace oboru**.
3. Pokud vybraný pracovní prostor ještě nemá Update Management nebo Change Tracking řešení, vytvoří se následující konfigurace oboru:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Pokud vybraný pracovní prostor už toto řešení obsahuje, řešení se znovu nenasazením a konfigurace oboru se nepřidá.

4. V některé z konfigurací vyberte tři tečky (**...**) a pak klikněte na **Upravit**. 
5. V podokně **Upravit obor konfigurace** vyberte **Vybrat skupiny počítačů**. V podokně **skupiny počítačů** se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru.

## <a name="saved-searches"></a>Uložená hledání

Když se počítač přidá do řešení Update Management, Change Tracking nebo inventáře, počítač se přidá do jednoho ze dvou uložených hledání ve vašem pracovním prostoru. Uložená hledání jsou dotazy, které obsahují počítače cílené na tato řešení.

Přejděte do svého pracovního prostoru. V části **Obecné**vyberte možnost **uložená hledání**. Dvě uložená hledání, která jsou používána těmito řešeními, jsou uvedena v následující tabulce:

|Název     |Kategorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Vyberte jedno z uložených hledání, abyste zobrazili dotaz, který se používá k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

![Uložená hledání](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Zrušení propojení s pracovním prostorem

Následující řešení jsou závislá na Log Analytics pracovním prostoru:

* [Update Management](automation-update-management.md)
* [Change Tracking](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal.  Než budete pokračovat, musíte nejprve odebrat dříve uvedená řešení. v opačném případě bude znemožněno pokračovat v tomto procesu. Projděte si článek pro konkrétní řešení, které jste naimportovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky a zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvořit automatizační prostředky a možná je budete muset před odpojením pracovního prostoru odebrat.

1. Z Azure Portal otevřete svůj účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

2. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. I když se Azure Automation pokusí odpojit účet Log Analytics pracovním prostoru, můžete sledovat průběh v nabídce **oznámení** .

Pokud jste použili řešení Update Management, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Aktualizace plánů – každá bude mít názvy, které odpovídají vytvořeným nasazením aktualizací.

* Skupiny hybridních pracovních procesů vytvořené pro řešení – každá bude pojmenována podobně jako machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste použili řešení Start/Stop VMs during off-hours, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Spuštění a zastavení plánů Runbook VM
* Spuštění a zastavení runbooků virtuálních počítačů
* Proměnné

Alternativně můžete také zrušit propojení pracovního prostoru s účtem Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního počítače z Update Management:

* Ve vašem pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání pro konfiguraci `MicrosoftDefaultScopeConfig-Updates`oboru. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
* Odeberte [agenta Log Analytics pro Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak je používat, přejděte k kurzům pro řešení:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – určení softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
