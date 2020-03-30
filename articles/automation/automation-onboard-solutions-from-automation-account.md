---
title: Zjistěte, jak zavést řešení pro správu aktualizací, sledování změn a inventář v Azure Automation
description: Zjistěte, jak zavést virtuální počítač Azure pomocí řešení pro správu aktualizací, sledování změn a inventáře, která jsou součástí Azure Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278672"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Palubní řešení správy aktualizací, sledování změn a zásob

Azure Automation poskytuje řešení pro správu aktualizací zabezpečení operačního systému, sledování změn a inventarizaci toho, co je nainstalováno ve vašich počítačích. Existuje mnoho způsobů, jak napalubě počítačů, můžete na palubě řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md), z vašeho účtu automatizace, nebo [runbook](automation-onboard-solutions.md). Tento článek popisuje registrace těchto řešení z vašeho účtu Automation.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Přejděte na svůj účet Automation a v části Správa **konfigurace**vyberte sledování **inventáře** nebo **změny** .

Zvolte pracovní prostor Log Analytics a účet Automatizace a kliknutím na **Povolit** povolíte řešení. Povolení řešení trvá přibližně 15 minut.

![Řešení palubního inventáře](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
>
> Seznam podporovaných dvojic mapování naleznete v tématu [Mapování oblasti pro účet automatizace a pracovní prostor Log Analytics](how-to/region-mappings.md).

Řešení Change Tracking a Inventory poskytují možnost na virtuálních počítačích [sledovat změny](automation-vm-change-tracking.md) a [inventář](automation-vm-inventory.md). V tomto kroku povolíte řešení na virtuálním počítači.

Po dokončení oznámení o sledování změn a inventáře vyberte v části **Správa aktualizací** **možnost Správa aktualizací** .

Řešení správy aktualizací umožňuje spravovat aktualizace a opravy pro vaše azure a hybridní virtuální počítače. Můžete posoudit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení a ověřit, zda na ně byly aktualizace úspěšně použity.

Na stránce povolit řešení je vybraný pracovní prostor Analýzy protokolů stejný pracovní prostor, který byl použit v předchozím kroku. Chcete-li přejít na **tlačítko Povolit,** klepněte na tlačítko Povolit. Povolení řešení trvá přibližně 15 minut.

![Řešení pro aktualizaci na palubě](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá konfiguraci oboru v rámci pracovního prostoru k cílení na počítače, které řešení získají. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která se používá k omezení rozsahu řešení na konkrétní počítače. Chcete-li získat přístup ke konfiguracím oboru, vyberte v účtu Automation v části **Související prostředky** **položku Pracovní prostor**. Potom v pracovním prostoru v části **Zdroje dat pracovního prostoru**vyberte Konfigurace **oboru**.

Pokud vybraný pracovní prostor ještě nemá řešení správy aktualizací nebo sledování změn, vytvoří se následující konfigurace oboru:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **Aktualizace MicrosoftDefaultScopeConfig**

Pokud vybraný pracovní prostor již řešení obsahuje, řešení se znovu nenasadí a konfigurace oboru se do něj nepřidá.

## <a name="saved-searches"></a>Uložená hledání

Když je počítač přidán do řešení Pro správu aktualizací nebo sledování změn a zásob, přidá se do jednoho ze dvou uložených hledání ve vašem pracovním prostoru. Tato uložená hledání jsou dotazy, které obsahují počítače, které jsou určeny pro tato řešení.

Přejděte do pracovního prostoru Log Analytics a v části **Obecné**vyberte **Uložená hledání** . Dvě uložená hledání používaná těmito řešeními jsou uvedena v následující tabulce:

|Name (Název)     |Kategorie  |Alias  |
|---------|---------|---------|
|Skupina MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|Skupina MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Výběrem uloženého hledání zobrazíte dotaz použitý k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

![Uložená hledání](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Na palubě virtuálních počítačů Azure

V části Účet automatizace vyberte v části **Správa konfigurace**nebo Správa aktualizací v části **Správa aktualizací**vyberte možnost **Inventář** nebo **sledování změn** nebo Správa **aktualizací** .

Klikněte na **+ Přidat virtuální počítače Azure**, vyberte jeden nebo více virtuálních počítačů ze seznamu. Virtuální počítače, které nelze povolit, jsou zašedlí a nelze je vybrat. Virtuální počítače Azure mohou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. Na stránce **Povolit správu aktualizací** klepněte na tlačítko **Povolit**. Tato akce přidá vybrané virtuální počítače do skupiny počítačů uložené hledání řešení.

![Povolení virtuálních počítačů Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Připojení počítače mimo Azure

Počítače, které nejsou v Azure, je třeba přidat ručně. V části Účet automatizace vyberte v části **Správa konfigurace**nebo Správa aktualizací v části **Správa aktualizací**vyberte možnost **Inventář** nebo **sledování změn** nebo Správa **aktualizací** .

Klikněte na **Přidat počítač, který není azure**. Tato akce otevře nové okno prohlížeče s [pokyny, jak nainstalovat a nakonfigurovat microsoft monitoring agent v počítači,](../azure-monitor/platform/log-analytics-agent.md) takže počítač může začít hlášení řešení. Pokud zapisujete počítač, který je aktuálně spravován operačním manažerem System Center, není vyžadován nový agent, informace o pracovním prostoru se zadávají do existujícího agenta.

## <a name="onboard-machines-in-the-workspace"></a>Palubní stroje v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které se už hlásí do vašeho pracovního prostoru, musí být přidány do Azure Automation, aby bylo možné povolit řešení. V části Účet automatizace vyberte v části **Správa konfigurace**nebo Správa aktualizací v části **Správa aktualizací**vyberte možnost **Inventář** nebo **sledování změn** nebo Správa **aktualizací** .

Vyberte **Spravovat počítače**. Tato akce otevře stránku **Spravovat počítače.** Tato stránka umožňuje povolit řešení na vybrané sadě počítačů, všech dostupných strojích nebo povolit řešení pro všechny aktuální počítače a povolit ho na všech budoucích počítačích. Tlačítko **Spravovat počítače** může být šedé, pokud jste dříve zvolili možnost **Povolit na všech dostupných a budoucích počítačích**.

![Uložená hledání](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Všechny dostupné stroje

Chcete-li povolit řešení pro všechny dostupné počítače, vyberte **možnost Povolit na všech dostupných počítačích**. Tato akce zakáže ovládací prvek pro přidání počítačů jednotlivě. Tato úloha přidá všechny názvy počítačů, které se hlásí do pracovního prostoru, do vyhledávacího dotazu uložené skupiny počítačů. Pokud je tato akce vybrána, zakáže **tlačítko Spravovat počítače.**

### <a name="all-available-and-future-machines"></a>Všechny dostupné a budoucí stroje

Chcete-li povolit řešení pro všechny dostupné a budoucí stroje, vyberte **možnost Povolit na všech dostupných i budoucích počítačích**. Tato možnost odstraní uložená hledání a konfigurace oboru z pracovního prostoru. Tato akce otevře řešení pro všechny počítače Azure a jiné než Azure, které jsou vykazování do pracovního prostoru. Pokud je tato akce vybrána, zakáže tlačítko **Spravovat počítače** trvale, protože nezbývá žádná konfigurace oboru.

Konfigurace oboru můžete přidat zpět přidáním počátečních uložených hledání zpět. Další informace naleznete v [tématu Uložená hledání](#saved-searches).

### <a name="selected-machines"></a>Vybrané stroje

Chcete-li povolit řešení pro jeden nebo více počítačů, vyberte **možnost Povolit na vybraných počítačích** a klepněte na tlačítko **Přidat** vedle každého počítače, který chcete přidat do řešení. Tato úloha přidá vybrané názvy počítačů do skupiny počítačů uložených vyhledávacídotaz pro řešení.

## <a name="unlink-workspace"></a>Zrušení propojení s pracovním prostorem

Následující řešení jsou závislá na pracovním prostoru Log Analytics:

* [Update Management](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete svůj účet odpojit přímo z portálu Azure.  Než budete pokračovat, musíte nejprve odstranit dříve uvedená řešení, jinak bude tento proces znemožněn. Projděte si článek konkrétního řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky k odpojení účtu automatizace.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení monitorování Azure SQL může mít vytvořené prostředky automatizace a může být také nutné odebrat před odpojením pracovního prostoru.

1. Na portálu Azure otevřete účet Automation a na stránce Účet automatizace vyberte **Propojený pracovní prostor** v části označené Související **prostředky** vlevo.

2. Na stránce Odpojit od propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušení propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Zobrazí se výzva k ověření, zda chcete pokračovat.

3. Zatímco Azure Automation se pokusí odpojit účet vašeho pracovního prostoru Log Analytics, můžete sledovat průběh v části **Oznámení** z nabídky.

Pokud jste použili řešení správy aktualizací, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Plány aktualizací – každý bude mít názvy, které odpovídají nasazení aktualizací, které jste vytvořili.

* Hybridní pracovní skupiny vytvořené pro řešení – každý bude pojmenován podobně jako machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste použili start a zastavit virtuální chod během mimo pracovní dobu řešení, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Spuštění a zastavení plánů runbooku virtuálních knih
* Spuštění a zastavení runbooků virtuálních knih
* Proměnné

Případně můžete také odpojit pracovní prostor od účtu Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet automatizace** v části **Související zdroje**. Na stránce Automation Account vyberte **Možnost Zrušit propojení účtu**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního virtuálního virtuálního montovazy ze správy aktualizací:

* V pracovním prostoru Log Analytics odeberte virtuální počítač z `MicrosoftDefaultScopeConfig-Updates`uloženého hledání konfigurace oboru . Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Pokračujte v kurzech o řešeních, kde se dozvíte, jak je používat.

* [Kurz – správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – poradce při potížích se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
