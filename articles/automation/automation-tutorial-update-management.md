---
title: Správa aktualizací a oprav pro virtuální počítače Azure v Azure Automation
description: V tomto článku se dozvíte, jak pomocí Update Management spravovat aktualizace a opravy pro virtuální počítače Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185785"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Správa aktualizací a oprav pro virtuální počítače Azure

Tento článek popisuje, jak můžete pomocí funkce Azure Automation [Update Management](automation-update-management.md) spravovat aktualizace a opravy pro virtuální počítače Azure. Informace o cenách najdete v tématu [ceny služby Automation pro Update Management](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> Update Management podporuje nasazení aktualizací od první strany a před stažením oprav. Tato podpora vyžaduje změny v systémech, které jsou opraveny. Informace o tom, jak nakonfigurovat tato nastavení v systémech, najdete v tématu [konfigurace web Windows Updateho nastavení Azure Automation Update Management](automation-configure-windows-update.md) .

Než začnete používat postupy v tomto článku, ujistěte se, že jste ve svých virtuálních počítačích povolili Update Management pomocí jednoho z následujících postupů:

* [Povolení Update Managementu z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [Povolení Update Management procházením Azure Portal](automation-onboard-solutions-from-browse.md)
* [Povolení Update Managementu z runbooku](automation-onboard-solutions.md)
* [Povolení Update Managementu z virtuálního počítače Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Omezení rozsahu nasazení

Update Management používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, aby přijímaly aktualizace. Další informace najdete v tématu [omezení rozsahu nasazení Update Management](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Zobrazení posouzení aktualizací:

1. Ve svém účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** . 

2. Aktualizace pro vaše prostředí jsou uvedené na stránce Správa aktualizací. Pokud se nějaké aktualizace identifikují jako chybějící, zobrazí se na kartě **chybějící aktualizace** seznam chybějících aktualizací.

3. V části **informační odkaz**vyberte odkaz pro aktualizaci, abyste otevřeli článek podpory, který vám poskytne důležité informace o této aktualizaci.

    ![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Kliknutím kamkoli jinde v aktualizaci otevřete podokno prohledávání protokolu. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Můžete upravit tento dotaz nebo vytvořit vlastní dotaz pro zobrazení podrobných informací.

    ![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

Pomocí následujících kroků nastavte výstrahy, které vám pomůžou zjistit stav nasazení aktualizace:

1. V účtu Automation přejděte na **výstrahy** v části **monitorování**a pak klikněte na **nové pravidlo výstrahy**.

2. Na stránce Vytvořit pravidlo výstrahy už je váš účet Automation vybraný jako prostředek. Pokud ho chcete změnit, klikněte na **Upravit prostředek**. 

3. Na stránce Vybrat prostředek zvolte **účty Automation** v rozevírací nabídce **filtrovat podle typu prostředku** . 

4. Vyberte účet Automation, který chcete použít, a potom klikněte na **Hotovo**.

5. Klikněte na **Přidat podmínku** a vyberte signál, který je vhodný pro nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o dvou dostupných signálech.

    |Název signálu|Dimenze|Description
    |---|---|---|
    |`Total Update Deployment Runs`|– Název nasazení aktualizace<br>-Status    |Upozorní na celkový stav nasazení aktualizace.|
    |`Total Update Deployment Machine Runs`|– Název nasazení aktualizace</br>-Status</br>– Cílový počítač</br>– Aktualizovat ID běhu nasazení    |Upozorní na stav nasazení aktualizace zaměřeného na konkrétní počítače.|

6. V poli dimenze vyberte platnou hodnotu ze seznamu. Pokud hodnota, kterou chcete, není v seznamu, klikněte na tlačítko **\+** vedle dimenze a zadejte vlastní název. Pak vyberte hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty pro dimenzi, klikněte na tlačítko **vybrat \* ** . Pokud nevyberete hodnotu dimenze, Update Management tuto dimenzi ignoruje.

    ![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

7. V části **logika výstrahy**zadejte hodnoty do polí **Časová agregace** a **prahová hodnota** a potom klikněte na **Hotovo**.

8. V dalším podokně zadejte název a popis výstrahy.

9. Nastavte pole **závažnost** na **informativní (závažnost 2)** pro úspěšné spuštění nebo **informativní (závažnost 1)** pro neúspěšné spuštění.

    ![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

10. V závislosti na tom, jak chcete pravidlo výstrahy povolit, klikněte na **Ano** nebo **ne**.

11. Pokud nechcete, aby se pro toto pravidlo zobrazovaly výstrahy, vyberte **potlačit výstrahy**.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurace skupin akcí pro vaše výstrahy

Jakmile budete mít nakonfigurovaná upozornění, můžete nastavit skupinu akcí, která je skupinou akcí pro použití v rámci více výstrah. Akce můžou zahrnovat e-mailová oznámení, Runbooky, Webhooky a spoustu dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/platform/action-groups.md).

1. Vyberte výstrahu a potom v části **skupiny akcí**vyberte **vytvořit novou** . 

2. Zadejte celé jméno a krátký název skupiny akcí. Při odesílání oznámení pomocí zadané skupiny používá Update Management krátký název.

3. V části **Akce**zadejte název, který určuje akci, například **e-mailové oznámení**. 

4. Jako **typ akce**vyberte vhodný typ, například **e-mail/SMS/Push/Voice**. 

5. Klikněte na **Upravit podrobnosti**.

6. Vyplňte podokno pro typ akce. Pokud například používáte **e-mail/SMS/Push/Voice**, zadejte název akce, zaškrtněte políčko **e-mail** , zadejte platnou e-mailovou adresu a klikněte na **OK**.

    ![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. V podokně Přidat skupinu akcí klikněte na tlačítko **OK**.

8. U e-mailu s výstrahou můžete přizpůsobit předmět e-mailu. V části **vytvořit pravidlo**vyberte **přizpůsobit akce** a pak vyberte **Předmět e-mailu**. 

9. Až budete hotovi, klikněte na **vytvořit pravidlo výstrahy**. 

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Při plánování nasazení aktualizace se vytvoří prostředek [plánu](shared-resources/schedules.md) propojený s runbookm **patch-MicrosoftOMSComputers** , který zpracovává nasazení aktualizace na cílových počítačích. Pro instalaci aktualizací musíte naplánovat nasazení, které následuje po plánu vydání a okně služby. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Pokud odstraníte prostředek plánu z Azure Portal nebo pomocí PowerShellu po vytvoření nasazení dojde k přerušení naplánovaného nasazení aktualizace a při pokusu o překonfigurování prostředků plánu z portálu se zobrazí chyba. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  

Naplánování nového nasazení aktualizace:

1. V účtu Automation v části **Správa aktualizací**klikněte na **Správa aktualizací** a pak vyberte **naplánovat nasazení aktualizací**.

2. V části **nové nasazení aktualizací**pomocí pole **název** zadejte jedinečný název pro vaše nasazení.

3. Vyberte operační systém, který má být cílem nasazení aktualizace.

4. V oblasti **skupiny pro aktualizaci (Preview)** definujte dotaz, který kombinuje předplatné, skupiny prostředků, umístění a značky, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. Další informace najdete v tématu [použití dynamických skupin s Update Management](automation-update-management-groups.md).

5. V rozevírací nabídce **počítače k aktualizaci** vyberte uložené výsledky hledání, importovanou skupinu nebo vyberte **počítače** a vyberte jednotlivé počítače. Pomocí této možnosti můžete zobrazit připravenost agenta Log Analytics pro každý počítač. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md).

6. Pomocí oblasti **klasifikace aktualizací** můžete určit [klasifikace aktualizací](automation-view-update-assessments.md#work-with-update-classifications) pro produkty. U každého produktu zrušte výběr všech podporovaných klasifikací aktualizací, ale těch, které se mají zahrnout do nasazení aktualizace.

7. Pomocí oblasti **zahrnutí/vyloučení aktualizací** můžete vybrat konkrétní aktualizace pro nasazení. Stránka zahrnutí/vyloučení zobrazuje aktualizace podle čísel ID článků znalostní báze, které mají být zahrnuty nebo vyloučeny. 
    
   > [!IMPORTANT]
   > Zapamatujte si, že vyloučení přepisují. Například pokud definujete pravidlo vyloučení `*` , Update Management vyloučí všechny opravy nebo balíčky z instalace. Vyloučené opravy se pořád na počítačích zobrazují jako chybějící. U počítačů se systémem Linux, pokud zahrnete balíček, který má vyloučený závislý balíček, Update Management nenainstaluje hlavní balíček.

   > [!NOTE]
   > Aktualizace, které se nahradily, se nedají zadat, aby se zahrnuly do nasazení aktualizace.

8. Vyberte **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

9. Pomocí pole **opakování** určete, jestli k nasazení dojde jednou, nebo použijte plán opakování a pak klikněte na **OK**.

10. V oblasti **pre-Scripts + post-Scripts (Preview)** vyberte skripty, které chcete spustit před a po nasazení. Další informace najdete v tématu [Správa předběžných skriptů a pozálohovacích skriptů](pre-post-scripts.md).
    
11. V poli časový **interval pro správu a údržbu** můžete zadat dobu, po kterou je možné nainstalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti:

    * Časové intervaly pro správu a údržbu určují, kolik aktualizací se instaluje.
    * Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
    * Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového období údržby.
    * Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, je často k instalaci aktualizace Service Pack trvat delší dobu.

    > [!NOTE]
    > Aby se aktualizace nepoužívaly mimo časové období údržby v Ubuntu, překonfigurujte `Unattended-Upgrade` balíček pro zákaz automatických aktualizací. Informace o tom, jak nakonfigurovat balíček, najdete v tématu [věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. V poli **Možnosti restartování** můžete určit způsob, jakým se má při nasazení zpracovávat restarty. Dostupné jsou tyto možnosti: 
    * V případě potřeby restartování (výchozí)
    * Vždy restartovat
    * Nikdy nerestartovat
    * Pouze restart; Tato možnost neinstaluje aktualizace.

    > [!NOTE]
    > Klíče registru uvedené v části [klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) můžou způsobit restart události, pokud jsou **Možnosti restartování** nastavené na **Nikdy nerestartovat**.

13. Až skončíte s konfigurací plánu nasazení, klikněte na **vytvořit**.

    ![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Vrátíte se na řídicí panel stavu. Vyberte **plánovaná nasazení aktualizací** , abyste zobrazili plán nasazení, který jste vytvořili.

## <a name="schedule-an-update-deployment-programmatically"></a>Naplánování nasazení aktualizace prostřednictvím kódu programu

Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). 

Pomocí ukázkové sady Runbook můžete vytvořit týdenní nasazení aktualizace. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Zkontroluje stav nasazení.

Po spuštění naplánovaného nasazení uvidíte jeho stav na kartě **nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **úspěch**. Pokud dojde k chybám s jednou nebo více aktualizacemi v nasazení, stav je **částečně neúspěšný**.

## <a name="view-results-of-a-completed-update-deployment"></a>Zobrazit výsledky dokončeného nasazení aktualizace

Až se nasazení dokončí, můžete ho vybrat a zobrazit jeho řídicí panel.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **výsledky aktualizace**najdete souhrn celkového počtu aktualizací a výsledků nasazení na cílových virtuálních počítačích. V tabulce na pravé straně se zobrazuje podrobný rozpis aktualizací a výsledků instalace pro každou z nich.

Dostupné hodnoty jsou:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože na základě definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Nevybráno** – aktualizace není vybraná pro nasazení.
* **Úspěch** – aktualizace byla úspěšná.
* **Chyba** – aktualizace se nezdařila.

Výběrem **všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Vyberte **výstup** , abyste viděli datový proud úlohy Runbooku zodpovědného za správu nasazení aktualizace na cílových virtuálních počítačích.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="view-the-deployment-alert"></a>Zobrazit výstrahu nasazení

Po dokončení nasazení aktualizace obdržíte výstrahu, kterou jste zadali během instalace pro nasazení. Tady je příklad e-mailu potvrzující opravu.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Další kroky

* Informace o konfiguracích oboru najdete v tématu [omezení rozsahu nasazení Update Management](automation-scope-configurations-update-management.md).
* Pokud potřebujete hledat v protokolech uložených v pracovním prostoru Log Analytics, přečtěte si téma [prohledávání protokolů v](../azure-monitor/log-query/log-query-overview.md)protokolech Azure monitor.
* Pokud jste dokončili nasazení, přečtěte si téma [zrušení propojení pracovního prostoru s účtem Automation pro Update Management](automation-unlink-workspace-update-management.md).
* Pokud chcete virtuální počítače odstranit z Update Management, přečtěte si téma [Odebrání virtuálních počítačů z Update Management](automation-remove-vms-from-update-management.md).
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](troubleshoot/update-management.md).
* Informace o řešení problémů s agentem Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Informace o řešení problémů s agentem aktualizací pro Linux najdete v tématu [řešení potíží s agentem aktualizace pro Linux](troubleshoot/update-agent-issues-linux.md).
