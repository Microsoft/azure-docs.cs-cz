---
title: Zjistěte, jak začlenění řešení Update Management, Change Tracking a Inventory ve službě Azure Automation
description: Zjistěte, jak připojit Azure virtuální počítač pomocí řešení Update Management, Change Tracking a Inventory, která jsou součástí služby Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: b91c3dec1dda401fbacab39325b2c8980ec7254f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625060"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Začlenění řešení Update Management, Change Tracking a Inventory

Azure Automation poskytuje řešení ke správě aktualizací zabezpečení operačního systému, sledování změn a inventáře, co je nainstalována na počítačích. Existuje mnoho způsobů, jak připojit počítače, můžete připojit řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md), ve svém účtu Automation, nebo podle [runbook](automation-onboard-solutions.md). Tento článek popisuje připojení těchto řešení ve svém účtu Automation.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Přejděte na svůj účet Automation a vyberte buď **inventáře** nebo **Change tracking** pod **CONFIGURATION MANAGEMENT**.

Zvolte pracovní prostor Log analytics a účet Automation a klikněte na tlačítko **povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

![Začlenění řešení Inventory](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Řešení Change Tracking a Inventory poskytují možnost na virtuálních počítačích [sledovat změny](automation-vm-change-tracking.md) a [inventář](automation-vm-inventory.md). V tomto kroku povolíte řešení na virtuálním počítači.

Po oznámení dokončení začlenění řešení Change Tracking a Inventory klikněte v části **SPRÁVA KONFIGURACE** na **Update Management**.

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Windows. Můžete vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích. Tato akce Povolit řešení pro váš virtuální počítač.

Vyberte **Správa aktualizací** pod **UPDATE MANAGEMENT**. Vybraný pracovní prostor Log Analytics je stejný jako pracovní prostor použitý v předchozím kroku. Kliknutím na **Povolit** začleňte řešení Update Management. Povolení řešení trvá přibližně 15 minut.

![Začlenění aktualizace řešení](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá konfiguraci oboru v pracovním prostoru pro cílové počítače, které získání řešení. Konfigurace oboru je skupina jednoho nebo více uložená hledání, který se používá k omezení oboru řešení tak, aby konkrétní počítače. Pro přístup k konfigurace oboru, ve vašem účtu Automation v části **související prostředky**vyberte **pracovní prostor**. V pracovním prostoru v části **zdroje dat pracovního prostoru**vyberte **konfigurace oboru**.

Pokud vybraný pracovní prostor dosud nemá řešení Update Management nebo Change Tracking, vytvoří se následující konfigurace oboru:

* **MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig aktualizace**

Pokud je vybraný pracovní prostor už řešení, se znovu nasadí řešení a konfigurace oboru se nepřidal do něj.

## <a name="saved-searches"></a>Uložené výsledky hledání

Když počítač se přidá do Update Management nebo Change Tracking a Inventory řešení, se přidají do jedné ze dvou uložené výsledky hledání ve vašem pracovním prostoru. Tyto uložené výsledky hledání jsou dotazy, které obsahují počítače, které cílí na tato řešení.

Přejděte do účtu Automation a vyberte **uložená hledání** pod **Obecné**. Dvě uložené výsledky hledání používat tato řešení můžete vidět v následující tabulce:

|Název     |Kategorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Vyberte buď uložené výsledky hledání, chcete-li zobrazit dotaz použitý k naplnění skupiny. Následující obrázek ukazuje dotaz a jeho výsledky:

![Uložené výsledky hledání](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Připojení virtuálních počítačů Azure

Z vaší automatizace účtu vyberte **inventáře** nebo **řešení Change tracking** pod **CONFIGURATION MANAGEMENT**, nebo **Správa aktualizací** v části **UPDATE MANAGEMENT**.

Klikněte na tlačítko **+ přidat virtuální počítače Azure**, vyberte jeden nebo více virtuálních počítačů ze seznamu. Virtuální počítače, které není možné jsou zobrazena šedě, out a nemůže být vybrán. Na **povolit řešení Update Management** klikněte na **povolit**. Tato akce přidá vybrané virtuální počítače do skupiny počítačů, které jsou uložené výsledky hledání pro řešení.

![Povolit virtuální počítače Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Připojení počítače mimo Azure

Počítače není v Azure je potřeba přidat ručně. Z vaší automatizace účtu vyberte **inventáře** nebo **řešení Change tracking** pod **CONFIGURATION MANAGEMENT**, nebo **Správa aktualizací** v části **UPDATE MANAGEMENT**.

Klikněte na tlačítko **přidat počítače umístěné mimo Azure**. Tato akce otevře nové okno prohlížeče se [pokyny o tom, jak nainstalovat a nakonfigurovat agenta Microsoft Monitoring Agent na počítači](../log-analytics/log-analytics-agent-overview.md) počítači mohli začít vytváření sestav do řešení. Pokud jste připojení počítač aktuálně spravován nástrojem System Center Operations Manager, bude nový agent není povinné, informace o pracovním prostoru se zadá do existujícího agenta.

## <a name="onboard-machines-in-the-workspace"></a>Připojit počítače v pracovním prostoru

Ručně nainstalovat počítače nebo počítače, které již hlásí do vašeho pracovního prostoru musí být přidán do Azure Automation pro řešení. aby byla povolená. Z vaší automatizace účtu vyberte **inventáře** nebo **řešení Change tracking** pod **CONFIGURATION MANAGEMENT**, nebo **Správa aktualizací** v části **UPDATE MANAGEMENT**.

Vyberte **spravovat počítače**. Tato akce otevře **Správa počítačů** stránky. Tato stránka umožňuje povolit řešení na vybraná sada počítačů, všech dostupných počítačích nebo povolit řešení pro všechny aktuální počítače a jeho povolení pro všechny budoucí počítače. **Spravovat počítače** může být tlačítko zašedlé, pokud jste dříve vybrali možnost **povolit na všech dostupných a budoucích počítačích**.

![Uložené výsledky hledání](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Všechny dostupné počítače

Chcete-li povolit řešení pro všechny dostupné počítače, vyberte **povolit na všech dostupných počítačích**. Tato akce zakáže ovládací prvek pro přidání počítače jednotlivě. Tato úloha přidá všechny názvy počítačů odesílajících sestavy do pracovního prostoru do skupiny počítačů uložený vyhledávací dotaz. Pokud je vybráno, tato akce zakáže **Správa počítačů** tlačítko.

### <a name="all-available-and-future-machines"></a>Všech dostupných a budoucích počítačích

Chcete-li povolit řešení všech dostupných i budoucích počítačích, vyberte **povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní z pracovního prostoru uložená hledání a konfigurace oboru. Tato akce otevře řešení tak, aby všechny Azure a jiných počítačích než Azure, která se hlásí k pracovnímu prostoru. Pokud je vybráno, tato akce zakáže **Správa počítačů** tlačítko trvale není žádná konfigurace oboru vlevo.

### <a name="selected-machines"></a>Vybrané počítače

Chcete-li povolit řešení pro jeden nebo více počítačů, vyberte **povolit na vybraných počítačích** a klikněte na tlačítko **přidat** vedle každého počítače, které chcete přidat do řešení. Tato úloha přidá názvy vybraných počítačů do skupiny počítačů uložený vyhledávací dotaz pro řešení.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Následující řešení jsou závislé na pracovní prostor Log Analytics:

* [Správa aktualizací](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že již nechcete integraci vašeho účtu Automation s Log Analytics, můžete svůj účet neodpojíte přímo z portálu Azure portal.  Než budete pokračovat, budete nejdřív muset odebrat řešení již bylo zmíněno dříve, jinak nebudou moct tento proces budete pokračovat. Přečtěte si článek pro konkrétní řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžou proveďte následující kroky se zrušit propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzí řešení monitorování Azure SQL pravděpodobně vytvořena prostředky služby automation a může také muset před rušení propojení pracovního prostoru odebrat.

1. Z portálu Azure portal otevřete svůj účet Automation, a na automatizaci účtu vyberte stránku **pracovní prostor propojený** části označené jako **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na tlačítko **zrušit propojení pracovního prostoru**.

   ![Zrušit propojení pracovního prostoru stránky](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli že chcete pokračovat.

3. Zatímco bude Azure Automation se pokusí zrušit propojení účtu pracovního prostoru Log Analytics, můžete sledovat průběh **oznámení** z nabídky.

Pokud jste použili řešení Update Management, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Plány aktualizace – každá bude mít názvy, které odpovídají vámi vytvořených nasazení aktualizací)

* Skupiny hybridních pracovních procesů vytvořené pro dané řešení – každá bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spuštění a zastavení virtuálních počítačů špičku, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Spouštět a zastavovat sady runbook plány virtuálního počítače
* Spuštění a zastavení sad runbook virtuálního počítače
* Proměnné

## <a name="next-steps"></a>Další postup

Kurzy k řešení se naučíte používat i nadále.

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)