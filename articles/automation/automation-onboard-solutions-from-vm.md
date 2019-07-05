---
title: Začlenění řešení Update Management, Change Tracking a Inventory z virtuálního počítače Azure
description: Zjistěte, jak k připojení virtuálního počítače Azure pomocí řešení Update Management, Change Tracking a Inventory, které jsou součástí služby Azure Automation.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 6b8693768e08f7ed80765015efa5af1a73b850c7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476602"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Začlenění řešení Update Management, Change Tracking a Inventory z virtuálního počítače Azure

Azure Automation poskytuje řešení, která vám pomohou spravovat aktualizace zabezpečení operačního systému, sledování změn a inventáře, co je nainstalována na počítačích. Počítače můžete připojit několika způsoby. Můžete připojit řešení z virtuálního počítače, [ve svém účtu Automation](automation-onboard-solutions-from-automation-account.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md), nebo pomocí [runbook](automation-onboard-solutions.md). Tento článek popisuje připojení těchto řešení z virtuálního počítače Azure.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-the-solutions"></a>Povolení řešení

Přejděte do existujícího virtuálního počítače. V části **operace**vyberte **Správa aktualizací**, **inventáře**, nebo **řešení Change tracking**. Virtuální počítač může existovat v libovolné oblasti bez ohledu na umístění účtu Automation. Po zprovoznění řešení z virtuálního počítače je potřeba mít `Microsoft.OperationalInsights/workspaces/read` oprávnění k určení, zda je virtuální počítač připojit k pracovnímu prostoru. Další informace o další oprávnění, která jsou obecně potřeba, najdete v článku [práva potřebná k připojení počítačů](automation-role-based-access-control.md#onboarding).

Povolte řešení pro virtuální počítač pouze, ujistěte se, že **povolit pro tento virtuální počítač** zaškrtnuto. Chcete-li připojit více počítačů k řešení, vyberte **povolit pro virtuální počítače v tomto předplatném**a pak vyberte **kliknutím vyberte počítače, které chcete povolit**. Další informace, jak připojit více počítačů najednou, najdete v článku [připojení Update Management, Change Tracking a Inventory řešení](automation-onboard-solutions-from-automation-account.md).

Vyberte pracovní prostor Azure Log Analytics a účet Automation a pak vyberte **povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

![Začlenění řešení Update Management](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Přejít na další řešení a pak vyberte **povolit**. Pracovní prostor Log Analytics a seznamy rozevíracího seznamu účtů Automation jsou zakázané, protože tato řešení použít stejný pracovní prostor a účet Automation jako povolený řešení.

> [!NOTE]
> **Sledování změn** a **inventáře** využívat stejná řešení. Když některé z těchto řešení je povolené, druhá je také povolena.

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá konfiguraci oboru v pracovním prostoru pro cílové počítače, které získání řešení. Konfigurace oboru je skupina jednoho nebo více uložená hledání, které umožňují omezit obor řešení, aby konkrétní počítače. Pro přístup k konfigurace oboru, ve vašem účtu Automation v části **související prostředky**vyberte **pracovní prostor**. V pracovním prostoru v části **zdroje dat pracovního prostoru**vyberte **konfigurace oboru**.

Pokud vybraný pracovní prostor už nemá řešení Update Management nebo Change Tracking, vytvoří se následující konfigurace oboru:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Pokud je vybraný pracovní prostor už řešení, se znovu nasadit řešení a nebude přidána konfigurace oboru.

Vyberte symbol tří teček ( **...** ) na všechny konfigurace a pak vyberte **upravit**. V **upravit konfiguraci oboru** vyberte **vyberte skupiny počítačů**. **Skupiny počítačů** podokně se zobrazí na uložená hledání, které se používají k vytvoření konfigurace oboru.

## <a name="saved-searches"></a>Uložená hledání

Při přidání počítače do Update Management, Change Tracking nebo řešení Inventory počítač přidán do jednoho ze dvou uložené výsledky hledání ve vašem pracovním prostoru. Uložené výsledky hledání jsou dotazy, které obsahují počítače, které cílí na tato řešení.

Přejděte do svého pracovního prostoru. V části **Obecné**vyberte **uložená hledání**. V následující tabulce jsou uvedeny dva uložená hledání, které jsou používány těmito řešeními:

|Název     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Vyberte jednu z uložené výsledky hledání, chcete-li zobrazit dotaz, který se používá k naplnění skupiny. Následující obrázek ukazuje dotaz a jeho výsledky:

![Uložená hledání](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Následující řešení jsou závislé na pracovní prostor Log Analytics:

* [Správa aktualizací](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že již nechcete integrace účtu služby Automation s pracovním prostorem Log Analytics, můžete svůj účet neodpojíte přímo z portálu Azure portal.  Než budete pokračovat, budete nejdřív muset odebrat řešení již bylo zmíněno dříve, jinak nebudou moct tento proces budete pokračovat. Přečtěte si článek pro konkrétní řešení, které jste importovali pochopit kroky nutné k jeho odebrání.

Po odebrání těchto řešení provedením následujících kroků se zrušit propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzí řešení monitorování Azure SQL pravděpodobně vytvořena prostředky služby automation a může také muset před rušení propojení pracovního prostoru odebrat.

1. Z portálu Azure portal otevřete svůj účet Automation, a na automatizaci účtu vyberte stránku **pracovní prostor propojený** části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na tlačítko **zrušit propojení pracovního prostoru**.

   ![Zrušit propojení pracovního prostoru stránky](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. Zatímco bude Azure Automation se pokusí zrušit propojení účtu pracovního prostoru Log Analytics, můžete sledovat průběh **oznámení** z nabídky.

Pokud jste použili řešení Update Management, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Plány aktualizace – každá bude mít názvy, které odpovídají vámi vytvořených nasazení aktualizací)

* Skupiny hybridních pracovních procesů vytvořené pro dané řešení – každá bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spouštění/zastavování virtuálních počítačů špičku, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Spouštět a zastavovat sady runbook plány virtuálního počítače
* Spuštění a zastavení sad runbook virtuálního počítače
* Proměnné

Také je můžete také zrušit propojení pracovního prostoru ve svém účtu Automation z pracovního prostoru Log Analytics. Ve svém pracovním prostoru, vyberte **účtu Automation** pod **související prostředky**. Na stránce účtu Automation vyberte **zrušení propojení účtu**.

## <a name="next-steps"></a>Další postup

Kurzy pro řešení se naučíte používat i nadále:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)
* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)
* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
