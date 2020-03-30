---
title: Palubní řešení správy aktualizací, sledování změn a inventáře z virtuálního počítače Azure
description: Zjistěte, jak založit virtuální počítač Azure pomocí řešení pro správu aktualizací, sledování změn a inventáře, která jsou součástí Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 621b429f5dc3a6b6620e4d41ad46763e1d4fa226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299518"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Palubní řešení správy aktualizací, sledování změn a inventáře z virtuálního počítače Azure

Azure Automation poskytuje řešení, která vám pomohou spravovat aktualizace zabezpečení operačního systému, sledovat změny a inventarizovat, co je nainstalované ve vašich počítačích. Existuje několik způsobů, jak na palubě strojů. Můžete zadat řešení z virtuálního počítače, [z vašeho účtu Automation](automation-onboard-solutions-from-automation-account.md), z procházení více [počítačů](automation-onboard-solutions-from-browse.md)nebo pomocí [runbooku](automation-onboard-solutions.md). Tento článek popisuje připojení těchto řešení z virtuálního počítače Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-the-solutions"></a>Povolit řešení

Nejprve povolte jedno nebo všechny tři řešení na vašem virtuálním počítači:

1. Na [portálu Azure](https://portal.azure.com)vyberte v levém podokně **virtuální počítače** nebo na **domovské** stránce vyberte **Virtuální počítače.**
2. Vyberte virtuální hod, pro který chcete povolit řešení.
3. Na stránce Virtuální počítač v části **Operace**vyberte **Položku Správa aktualizací**, **Inventář**nebo **Sledování změn**. Virtuální počítač může existovat v libovolné oblasti bez ohledu na umístění vašeho účtu automatizace. Při registraci řešení z virtuálního počítači, `Microsoft.OperationalInsights/workspaces/read` musíte mít oprávnění k určení, pokud je virtuální počítač na palubě do pracovního prostoru. Další informace o dalších oprávněních, která jsou požadována, naleznete [v tématu oprávnění potřebná pro palubní počítače](automation-role-based-access-control.md#onboarding).

Informace o tom, jak zavést více počítačů najednou, najdete [v tématu Řešení pro správu aktualizací, sledování změn a zásoby](automation-onboard-solutions-from-automation-account.md).

Vyberte pracovní prostor Azure Log Analytics a účet Automatizace a pak vyberte **Povolit,** chcete-li povolit řešení. Povolení řešení trvá přibližně 15 minut.

![Na palubě řešení správy aktualizací](media/automation-tutorial-update-management/manageupdates-update-enable.png)

Přejděte na jiná řešení a pak vyberte **Povolit**. Rozevírací seznamy pracovního prostoru Analýzy protokolů a účtu Automatizace jsou zakázány, protože tato řešení používají stejný účet pracovního prostoru a automatizace jako dříve povolené řešení.

> [!NOTE]
> **Sledování změn** a **zásoby** používají stejné řešení. Pokud je jedno z těchto řešení povoleno, druhé je také povoleno.

## <a name="scope-configuration"></a>Konfigurace oboru

Každé řešení používá konfiguraci oboru v pracovním prostoru k cílení na počítače, které řešení získají. Konfigurace oboru je skupina jednoho nebo více uložených hledání, které se používají k omezení rozsahu řešení na konkrétní počítače. Chcete-li získat přístup ke konfiguracím oboru, vyberte v části **Související prostředky**v části Související prostředky **položku Pracovní prostor**. V pracovním prostoru vyberte v části **Zdroje dat pracovního prostoru** **položku Konfigurace oboru**.

Pokud vybraný pracovní prostor ještě nemá řešení správa aktualizací nebo sledování změn, vytvoří se následující konfigurace oboru:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **Aktualizace MicrosoftDefaultScopeConfig**

Pokud vybraný pracovní prostor již řešení má, řešení není znovu nasazeno a konfigurace oboru se nepřidá.

Vyberte tři tečky (**...**) v libovolné konfiguraci a pak vyberte **Upravit**. V podokně **Upravit konfigurace oboru** vyberte **Vybrat skupiny počítačů**. Podokno **Skupiny počítačů** zobrazuje uložená hledání, která se používají k vytvoření konfigurace oboru.

## <a name="saved-searches"></a>Uložená hledání

Když je počítač přidán do řešení Správa aktualizací, Sledování změn nebo Inventář, počítač se přidá do jednoho ze dvou uložených hledání ve vašem pracovním prostoru. Uložená hledání jsou dotazy, které obsahují počítače, které jsou určeny pro tato řešení.

Přejděte do svého pracovního prostoru. V části **Obecné**vyberte **Uložená hledání**. Dvě uložená hledání, která jsou používána těmito řešeními, jsou uvedena v následující tabulce:

|Name (Název)     |Kategorie  |Alias  |
|---------|---------|---------|
|Skupina MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|Skupina MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

Výběrem některého z uložených hledání zobrazíte dotaz, který se používá k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

![Uložená hledání](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Zrušení propojení s pracovním prostorem

Následující řešení jsou závislá na pracovním prostoru Log Analytics:

* [Update Management](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete svůj účet odpojit přímo z portálu Azure.  Než budete pokračovat, musíte nejprve odstranit dříve uvedená řešení, jinak bude tento proces znemožněn. Projděte si článek konkrétního řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky k odpojení účtu automatizace.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení monitorování Azure SQL může mít vytvořené prostředky automatizace a může být také nutné odebrat před odpojením pracovního prostoru.

1. Na webu Azure Portal otevřete účet Automation a na stránce Účet automatizace vyberte **Propojený pracovní prostor** v části Související **prostředky** na levé straně.

2. Na stránce Odpojit od propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušení propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. Zatímco Azure Automation se pokusí odpojit účet vašeho pracovního prostoru Log Analytics, můžete sledovat průběh v části **Oznámení** z nabídky.

Pokud jste použili řešení správy aktualizací, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Plány aktualizací – každý bude mít názvy, které odpovídají nasazení aktualizací, které jste vytvořili.

* Hybridní pracovní skupiny vytvořené pro řešení – každý bude pojmenován podobně jako machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste použili spuštění a zastavení virtuálních her během mimo pracovní dobu řešení, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Spuštění a zastavení plánů runbooku virtuálních knih
* Spuštění a zastavení runbooků virtuálních knih
* Proměnné

Případně můžete také odpojit pracovní prostor od účtu Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet automatizace** v části **Související zdroje**. Na stránce Automation Account vyberte **Možnost Zrušit propojení účtu**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního virtuálního virtuálního montovazy ze správy aktualizací:

* V pracovním prostoru Log Analytics odeberte virtuální počítač z `MicrosoftDefaultScopeConfig-Updates`uloženého hledání konfigurace oboru . Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Pokračujte v kurzech řešení, kde se dozvíte, jak je používat:

* [Kurz – správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – poradce při potížích se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
