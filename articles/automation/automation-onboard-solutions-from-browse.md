---
title: Zjistěte, jak zavést řešení pro správu aktualizací, sledování změn a inventář pro více virtuálních počítačů v Azure Automation
description: Zjistěte, jak zavést virtuální počítač Azure pomocí řešení pro správu aktualizací, sledování změn a inventáře, která jsou součástí Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 385806dca7dcac9fd0aac4c1bf9e1072e7fe5ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979477"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Povolení řešení správy aktualizací, sledování změn a inventáře na více virtuálních počítačích

Azure Automation poskytuje řešení pro správu aktualizací zabezpečení operačního systému, sledování změn a inventarizaci toho, co je nainstalováno ve vašich počítačích. Existuje několik způsobů, jak napalubě počítače, můžete na palubě řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), z [vašeho účtu automatizace](automation-onboard-solutions-from-automation-account.md), při procházení virtuálních počítačů, nebo [runbook](automation-onboard-solutions.md). Tento článek popisuje registrace těchto řešení při procházení virtuálních počítačů v Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Na webu Azure Portal přejděte na **Virtuální počítače**.

Pomocí zaškrtávacích políček vyberte virtuální počítače, které chcete nastoupit pomocí funkce Sledování změn a zásob nebo Správy aktualizací. Registrace je k dispozici až pro tři různé skupiny prostředků najednou. Virtuální počítače Azure mohou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation.

![Seznam virtuálních měn](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Pomocí ovládacích prvků filtru upravte seznam virtuálních počítačů a kliknutím na horní políčko vyberte všechny virtuální počítače v seznamu.

Na panelu příkazů klepněte na **položku Služby** a vyberte možnost **Sledování změn**, **Inventář**nebo **Správa aktualizací**.

> [!NOTE]
> **Sledování změn** a **zásoby** používají stejné řešení, pokud je povolena jedna druhá, je povolena také.

Následující obrázek je určen pro správu aktualizací. Sledování změn a zásoby mají stejné rozložení a chování.

Seznam virtuálních počítačů je filtrován tak, aby zobrazoval pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud jsou vaše virtuální počítače ve více než třech skupinách prostředků, jsou vybrány první tři skupiny prostředků.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Omezení registrace

Počet skupin prostředků, které můžete použít pro připojení, je omezen [limity nasazení Správce prostředků](../azure-resource-manager/templates/cross-resource-group-deployment.md). Nasazení Správce prostředků, která nelze zaměňovat s nasazeními aktualizace, jsou omezena na 5 skupin prostředků na jedno nasazení. Aby byla zajištěna integrita registrace, jsou dvě z těchto skupin prostředků vyhrazeny ke konfiguraci pracovního prostoru Log Analytics, účtu automatizace a souvisejících prostředků. To vám ponechá 3 skupiny prostředků, které můžete vybrat pro nasazení. Toto omezení platí pouze pro souběžné registrace, nikoli na počet skupin prostředků, které lze spravovat pomocí řešení automatizace.

Můžete také použít runbook pro onboarding, další informace, najdete [v tématu onboarding aktualizace a řešení sledování změn na Azure Automation](automation-onboard-solutions.md).

Pomocí ovládacích prvků filtru vyberte virtuální počítače z různých předplatných, umístění a skupin prostředků.

![Řešení pro správu aktualizací na palubě](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Zkontrolujte možnosti pracovního prostoru Analýzy protokolů a účtu Automatizace. Ve výchozím nastavení je vybrán existující účet pracovního prostoru a automatizace. Pokud chcete použít jiný pracovní prostor Analýzy protokolů a účet automatizace, klikněte na **vlastní** a vyberte je na stránce **Vlastní konfigurace.** Když zvolíte pracovní prostor Log Analytics, je provedena kontrola k určení, zda je propojen s účtem automatizace. Pokud je nalezen propojený účet automatizace, zobrazí se následující obrazovka. Po dokončení klepněte na tlačítko **OK**.

![Výběr pracovního prostoru a účtu](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Pokud vybraný pracovní prostor není propojen s účtem automatizace, zobrazí se následující obrazovka. Vyberte účet automatizace a po dokončení klepněte na **tlačítko OK.**

![Žádný pracovní prostor](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
>
> Seznam podporovaných dvojic mapování naleznete v tématu [Mapování oblasti pro účet automatizace a pracovní prostor Log Analytics](how-to/region-mappings.md).

Zrušte zaškrtnutí políčka vedle libovolného virtuálního počítače, který nechcete povolit. Virtuální počítače, které nelze povolit, už nejsou vybrané.

Chcete-li povolit **řešení,** klepněte na tlačítko Povolit. Povolení řešení trvá přibližně 15 minut.

## <a name="unlink-workspace"></a>Zrušení propojení s pracovním prostorem

Následující řešení jsou závislá na pracovním prostoru Log Analytics:

* [Update Management](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete svůj účet odpojit přímo z portálu Azure. Než budete pokračovat, musíte nejprve odstranit dříve uvedená řešení, jinak bude tento proces znemožněn. Projděte si článek konkrétního řešení, které jste importovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky k odpojení účtu automatizace.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení monitorování Azure SQL může mít vytvořené prostředky automatizace a může být také nutné odebrat před odpojením pracovního prostoru.

1. Na webu Azure Portal otevřete účet Automation a na stránce Účet automatizace vyberte **Propojený pracovní prostor** v části Související **prostředky** na levé straně.

2. Na stránce Odpojit od propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušení propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. Zatímco Azure Automation se pokusí odpojit účet vašeho pracovního prostoru Log Analytics, můžete sledovat průběh v části **Oznámení** z nabídky.

Pokud jste použili řešení správy aktualizací, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Plány aktualizací – každý bude mít názvy, které odpovídají vytvořeným nasazením aktualizací)

* Hybridní pracovní skupiny vytvořené pro řešení – každý bude pojmenován podobně jako machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste použili spuštění a zastavení virtuálních her během mimo pracovní dobu řešení, volitelně můžete chtít odebrat následující položky, které již nejsou potřeba po odebrání řešení.

* Spuštění a zastavení plánů runbooku virtuálních knih
* Spuštění a zastavení runbooků virtuálních knih
* Proměnné

Případně můžete také odpojit pracovní prostor od účtu Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet automatizace** v části **Související zdroje**. Na stránce Automation Account vyberte **Možnost Zrušit propojení účtu**.

## <a name="troubleshooting"></a>Řešení potíží

Při připojování více počítačů mohou existovat počítače, které se zobrazují jako **Nelze povolit**. Existují různé důvody, proč některé počítače nemusí být povolena. Následující části ukazují možné důvody pro **nelze povolit** stav na virtuálním počítači při pokusu o připojení.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Sestavy virtuálních virtuálních montovek do jiného pracovního prostoru: '\<název_pracovního_\>  Změnit konfiguraci tak, aby ji používala k povolení

**Příčina**: Tato chyba ukazuje, že virtuální počítač, který se pokoušíte napalubě sestavy do jiného pracovního prostoru.

**Řešení:** Kliknutím na **použít jako konfiguraci** můžete změnit cílový pracovní prostor Automatizační účet a Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Sestavy virtuálních aplikací do pracovního prostoru, který není v tomto předplatném k dispozici

**Příčina**: Pracovní prostor, kterému virtuální počítač hlásí:

* Je v jiném předplatném, nebo
* Již neexistuje, nebo
* Je ve skupině prostředků, ke které nemáte přístupová oprávnění

**Řešení**: Najděte účet automatizace přidružený k pracovnímu prostoru, který virtuální počítač podá do virtuálního počítače a za ním změnou konfigurace oboru.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Verze nebo distribuce operačního systému virtuálního aplikace není podporovaná.

**Příčina:** Řešení není podporováno pro všechny distribuce Linuxu nebo pro všechny verze systému Windows.

**Řešení:** Řešení naleznete v [seznamu podporovaných klientů.](automation-update-management.md#clients)

### <a name="classic-vms-cannot-be-enabled"></a>Klasické virtuální aplikace nejde povolit.

**Příčina**: Virtuální počítače, které používají klasický model nasazení, nejsou podporovány.

**Řešení:** Migrujte virtuální počítač do modelu nasazení Správce prostředků. Informace o tom, jak to provést, naleznete [v tématu Migrace prostředků modelu klasického nasazení](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuální ms je zastaveno. (deallocated)

**Příčina**: Virtuální počítač není ve **stavu Spuštění.**

**Řešení**: K naplnění virtuálního počítače k řešení musí být spuštěný virtuální počítač. Kliknutím na vložkový odkaz **Start VM** spusťte virtuální ho, aniž byste museli přejít od stránky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního virtuálního virtuálního montovazy ze správy aktualizací:

* V pracovním prostoru Log Analytics odeberte virtuální počítač z `MicrosoftDefaultScopeConfig-Updates`uloženého hledání konfigurace oboru . Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Teď, když je řešení povolené pro vaše virtuální počítače, navštivte článek přehled správy aktualizací, kde se dozvíte, jak vytvořit **nasazení aktualizace** pro vaše počítače.

> [!div class="nextstepaction"]
> [Správa aktualizací – správa aktualizací a oprav pro virtuální počítače Azure](./automation-tutorial-update-management.md)

Kromě návody na řešení a jak je používat:

* [Kurz – správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – poradce při potížích se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
