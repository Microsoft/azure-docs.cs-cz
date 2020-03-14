---
title: Naučte se integrovat řešení Update Management, Change Tracking a inventáře v Azure Automation
description: Naučte se, jak připojit virtuální počítač Azure pomocí Update Management, Change Tracking a řešení inventáře, která jsou součástí Azure Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278672"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Zprovoznění řešení Update Management, Change Tracking a inventáře

Azure Automation poskytuje řešení pro správu aktualizací zabezpečení operačního systému, sledování změn a inventarizaci toho, co je na vašich počítačích nainstalované. Existuje mnoho způsobů, jak připojit počítače, můžete připojit řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), [Procházet více počítačů](automation-onboard-solutions-from-browse.md), z účtu Automation nebo pomocí [Runbooku](automation-onboard-solutions.md). Tento článek popisuje připojování těchto řešení z vašeho účtu Automation.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Přejděte do svého účtu Automation a v části **Správa konfigurace**vyberte buď **inventarizaci** , nebo **sledování změn** .

Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

![Řešení inventáře k zprovoznění](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
>
> Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

Řešení Change Tracking a Inventory poskytují možnost na virtuálních počítačích [sledovat změny](automation-vm-change-tracking.md) a [inventář](automation-vm-inventory.md). V tomto kroku povolíte řešení na virtuálním počítači.

Až se dokončí oznámení o registraci řešení Change Tracking a Inventory, vyberte v části **Správa aktualizací**možnost **Správa aktualizací** .

Řešení Update Management umožňuje spravovat aktualizace a opravy pro Azure a hybridní virtuální počítače. Můžete posoudit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení, abyste ověřili, že aktualizace byly do nich úspěšně aplikovány.

Na stránce povolit řešení je vybraný pracovní prostor Log Analytics stejným pracovním prostorem použitým v předchozím kroku. Klikněte na **Povolit** , aby se připojilo řešení Update Management. Povolení řešení trvá přibližně 15 minut.

![Připojení řešení Update](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, které řešení obdrží. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu řešení na konkrétní počítače. Pokud chcete získat přístup k konfiguracím oboru, vyberte v účtu Automation v části **související prostředky**možnost **pracovní prostor**. Pak v pracovním prostoru v části **zdroje dat pracovního prostoru**vyberte **Konfigurace oboru**.

Pokud vybraný pracovní prostor nemá zatím Update Management nebo Change Tracking řešení, vytvoří se následující konfigurace oboru:

* **MicrosoftDefaultScopeConfig – sledování změn ve**

* **MicrosoftDefaultScopeConfig – aktualizace**

Pokud vybraný pracovní prostor už toto řešení obsahuje, řešení se znovu nenasadit a do něj se nepřidá konfigurace oboru.

## <a name="saved-searches"></a>Uložená hledání

Když se počítač přidá do Update Management nebo řešení Change Tracking a inventáře, přidají se do jednoho ze dvou uložených hledání ve vašem pracovním prostoru. Tato uložená hledání jsou dotazy, které obsahují počítače cílené na tato řešení.

Přejděte do pracovního prostoru Log Analytics a v části **Obecné**vyberte **uložená hledání** . Dvě uložená hledání, která používají tato řešení, se dají zobrazit v následující tabulce:

|Název     |Kategorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Pokud chcete zobrazit dotaz, který jste použili k naplnění skupiny, vyberte buď uložená hledání. Následující obrázek znázorňuje dotaz a jeho výsledky:

![Uložená hledání](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Zprovoznění virtuálních počítačů Azure

V účtu Automation vyberte v části **Správa konfigurace** **inventarizaci** nebo **sledování změn** , nebo **Správa aktualizací** pod **správou aktualizací**.

Klikněte na **+ Přidat virtuální počítače Azure**, v seznamu vyberte jeden nebo víc virtuálních počítačů. Virtuální počítače, které není možné povolit, jsou šedé a nelze je vybrat. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. Na stránce **povolit Update Management** klikněte na **Povolit**. Tato akce přidá vybrané virtuální počítače do skupiny počítačů uloženého hledání řešení.

![Povolení virtuálních počítačů Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Připojení počítače mimo Azure

Počítače, které nejsou v Azure, se musí přidat ručně. V účtu Automation vyberte v části **Správa konfigurace** **inventarizaci** nebo **sledování změn** , nebo **Správa aktualizací** pod **správou aktualizací**.

Klikněte na **Přidat jiný počítač než Azure**. Tato akce otevře nové okno prohlížeče s [pokyny, jak nainstalovat a nakonfigurovat Microsoft Monitoring Agent v počítači](../azure-monitor/platform/log-analytics-agent.md) , aby mohl počítač zahájit vytváření sestav do řešení. Pokud se připojujete k počítači, který je aktuálně spravovaný pomocí System Center Operations Manager, není vyžadován nový agent, informace o pracovním prostoru jsou zadány do stávajícího agenta.

## <a name="onboard-machines-in-the-workspace"></a>Zprovoznění počítačů v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které už hlásí do vašeho pracovního prostoru, se musí přidat do Azure Automation, aby bylo řešení povolené. V účtu Automation vyberte v části **Správa konfigurace** **inventarizaci** nebo **sledování změn** , nebo **Správa aktualizací** pod **správou aktualizací**.

Vyberte **spravovat počítače**. Tato akce otevře stránku **Správa počítačů** . Tato stránka umožňuje povolit řešení na vybrané sadě počítačů, všech dostupných počítačích nebo povolit řešení pro všechny aktuální počítače a povolit ho na všech budoucích počítačích. Tlačítko **spravovat počítače** může být šedé, pokud jste předtím zvolili možnost **Povolit u všech dostupných a budoucích počítačů**.

![Uložená hledání](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Všechny dostupné počítače

Pokud chcete řešení povolit pro všechny dostupné počítače, vyberte **Povolit na všech dostupných počítačích**. Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě. Tato úloha přidá všechny názvy počítačů, které nahlásí do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů. Když se tato akce vybere, zakáže se tlačítko **spravovat počítače** .

### <a name="all-available-and-future-machines"></a>Všechny dostupné a budoucí počítače

Pokud chcete řešení povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uložená hledání a konfigurace oboru z pracovního prostoru. Tato akce otevře řešení pro všechny počítače Azure a mimo Azure, které vytvářejí sestavy do pracovního prostoru. Když se tato akce vybere, zakáže se tlačítko **spravovat počítače** trvale, protože není k dispozici žádná konfigurace oboru.

Konfiguraci oboru můžete přidat zpátky přidáním počátečních uložených hledání zpět. Další informace najdete v tématu [uložená hledání](#saved-searches).

### <a name="selected-machines"></a>Vybrané počítače

Pokud chcete řešení povolit pro jeden nebo více počítačů, vyberte **Povolit na vybraných počítačích** a klikněte na **Přidat** vedle každého počítače, který chcete přidat do řešení. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro dané řešení.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Následující řešení jsou závislá na Log Analytics pracovním prostoru:

* [Správa aktualizací](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal.  Než budete pokračovat, musíte nejprve odebrat dříve uvedená řešení. v opačném případě bude znemožněno pokračovat v tomto procesu. Projděte si článek pro konkrétní řešení, které jste naimportovali, abyste pochopili kroky potřebné k jeho odebrání.

Až tato řešení odeberete, můžete provést následující kroky a zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvořit automatizační prostředky a možná je budete muset před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation a na stránce účet Automation vyberte **propojený pracovní prostor** v části označené **příbuzné prostředky** vlevo.

2. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, zda chcete pokračovat.

3. I když se Azure Automation pokusí zrušit propojení účtu s Log Analytics pracovním prostorem, můžete sledovat průběh v nabídce **oznámení** .

Pokud jste použili řešení Update Management, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Aktualizace plánů – každá bude mít názvy, které odpovídají vytvořeným nasazením aktualizací.

* Skupiny hybridních pracovních procesů vytvořené pro řešení – každá bude pojmenována podobně jako machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste v době mimo špičku použili virtuální počítače spustit a zastavit, budete možná chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Spuštění a zastavení plánů Runbook VM
* Spuštění a zastavení runbooků virtuálních počítačů
* Proměnné

Alternativně můžete také zrušit propojení pracovního prostoru s účtem Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního počítače z Update Management:

* Ve vašem pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání pro `MicrosoftDefaultScopeConfig-Updates`konfigurace oboru. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak je používat, přejděte k kurzům řešení.

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – určení softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
