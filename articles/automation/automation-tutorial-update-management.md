---
title: Správa aktualizací a oprav pro virtuální počítače Azure
description: Tento článek poskytuje přehled o tom, jak pomocí Azure Automation Update Management spravovat aktualizace a opravy pro virtuální počítače Azure a mimo Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604681"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Správa aktualizací a oprav pro virtuální počítače Azure

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače. V tomto kurzu se naučíte rychle vyhodnotit stav dostupných aktualizací, plánovat instalaci požadovaných aktualizací, kontrolovat výsledky nasazení a vytvořit upozornění za účelem ověření správného použití aktualizací.

Informace o cenách najdete v tématu [ceny služby Automation pro Update Management](https://azure.microsoft.com/pricing/details/automation/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Řešení [Update Management](automation-update-management.md) povolené pro jeden nebo více vašich virtuálních počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení Update Management se otevře stránka Správa aktualizací. Pokud se nějaké aktualizace identifikují jako chybějící, zobrazí se na kartě **chybějící aktualizace** seznam chybějících aktualizací.

V části **informační odkaz**vyberte odkaz aktualizace a otevřete tak článek podpory pro aktualizaci. Důležité informace o této aktualizaci najdete v části.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknutím kamkoli jinde v aktualizaci otevřete podokno hledání protokolu pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Můžete upravit tento dotaz nebo vytvořit vlastní dotaz pro zobrazení podrobných informací o nasazených nebo chybějících aktualizacích ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

V tomto kroku se naučíte nastavit výstrahu, která vám umožní zjistit stav nasazení aktualizace.

### <a name="alert-conditions"></a>Podmínky upozornění

V účtu Automation přejděte na **výstrahy** v části **monitorování**a pak klikněte na **nové pravidlo výstrahy**.

Váš účet Automation už je vybraný jako prostředek. Pokud ho chcete změnit, klikněte na **Vybrat**. Na stránce Vybrat prostředek zvolte **účty Automation** v rozevírací nabídce **filtrovat podle typu prostředku** . Vyberte svůj účet Automation a potom klikněte na **Hotovo**.

Klikněte na **Přidat podmínku** a vyberte signál, který je vhodný pro nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o dvou dostupných signálech.

|Název signálu|Dimenze|Popis|
|---|---|---|
|`Total Update Deployment Runs`|– Název nasazení aktualizace<br>-Status|Tento signál se upozorní na celkový stav nasazení aktualizace.|
|`Total Update Deployment Machine Runs`|– Název nasazení aktualizace</br>-Status</br>– Cílový počítač</br>– Aktualizovat ID běhu nasazení|Tento signál se upozorní na stav nasazení aktualizace zaměřeného na konkrétní počítače.|

V poli dimenze vyberte platnou hodnotu ze seznamu. Pokud hodnota, kterou chcete, není v seznamu, klikněte na **\+** znaménko vedle dimenze a zadejte vlastní název. Pak vyberte hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty pro dimenzi, klikněte na tlačítko **vybrat \* ** . Pokud nevyberete hodnotu dimenze, Update Management tuto dimenzi ignoruje.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **Logika upozornění** jako **Prahová hodnota** zadejte **1**. Jakmile budete hotovi, vyberte **Hotovo**.

### <a name="alert-details"></a>Podrobnosti upozornění

V části **2. Definujte podrobnosti výstrahy**, zadejte název a popis výstrahy. Nastavte **Závažnost** na **Informativní (záv. 2)** pro úspěšné spuštění a **Informativní (záv. 1)** pro neúspěšné spuštění.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **skupiny akcí**vyberte **vytvořit novou**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Akce můžou zahrnovat e-mailová oznámení, Runbooky, Webhooky a spoustu dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/platform/action-groups.md).

Do pole **název skupiny akcí** zadejte název výstrahy a krátký název. Při odesílání oznámení pomocí zadané skupiny používá Update Management krátký název místo úplného názvu skupiny akcí.

V části **Akce**zadejte název akce, například **e-mailové oznámení**. Jako **typ akce**vyberte **e-mail/SMS/Push/Voice**. Pokud chcete zobrazit **Podrobnosti**, vyberte **Upravit podrobnosti**.

V podokně E-mailové/SMS/nabízené/hlasové oznámení zadejte název. Zaškrtněte políčko **e-mail** a zadejte platnou e-mailovou adresu.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

V podokně E-mail/SMS/oznámení/hlas klikněte na tlačítko **OK**. V podokně Přidat skupinu akcí klikněte na tlačítko **OK**.

Chcete-li přizpůsobit předmět e-mailu s výstrahou, v části **vytvořit pravidlo**v části **Upravit akce**vyberte **Předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Výstraha vás upozorní, když se nasazení aktualizace zdaří a které počítače byly součástí běhu nasazení aktualizace.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Dále naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro instalaci aktualizací. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Při plánování nasazení aktualizace se vytvoří prostředek [plánu](shared-resources/schedules.md) propojený s runbookm **patch-MicrosoftOMSComputers** , který zpracovává nasazení aktualizace na cílových počítačích. Pokud odstraníte prostředek plánu z Azure Portal nebo pomocí PowerShellu po vytvoření nasazení dojde k přerušení naplánovaného nasazení aktualizace a při pokusu o překonfigurování prostředků plánu z portálu se zobrazí chyba. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  

Pokud chcete pro virtuální počítač naplánovat nové nasazení aktualizací, přejděte do části **Správa aktualizací** a pak vyberte **Naplánovat nasazení aktualizací**.

V části **Nové nasazení aktualizací** zadejte následující informace:

* **Název:** Zadejte jedinečný název nasazení aktualizací.

* **Operační systém:** Vyberte cílový operační systém pro nasazení aktualizací.

* **Skupiny, které se mají aktualizovat (Preview)**: Definujte dotaz, který kombinuje předplatné, skupiny prostředků, umístění a značky a vytvoří dynamickou skupinu virtuálních počítačů Azure, které se mají zahrnout do nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management-groups.md).

* **Počítače, které se mají aktualizovat**: v rozevírací nabídce vyberte uložené hledání, importovanou skupinu nebo vyberte **počítače** . pak vyberte jednotlivé počítače. Pokud zvolíte možnost **počítače**, připravenost jednotlivých počítačů se zobrazí ve sloupci **připravenosti agenta aktualizace** . Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md).

* **Klasifikace aktualizace**: pro každý produkt zrušte výběr všech podporovaných klasifikací aktualizací, ale těch, které se mají zahrnout do nasazení aktualizace. Pro tento kurz ponechte všechny typy vybrané pro všechny produkty.

  Typy klasifikace jsou:

   |Operační systém  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace<br>Ovladač        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popisy typů klasifikace najdete v tématu [klasifikace aktualizací](automation-view-update-assessments.md#update-classifications).

* **Aktualizace k zahrnutí/vyloučení** – otevře stránku zahrnutí/vyloučení. Aktualizace, které mají být zahrnuty nebo vyloučeny, jsou na různých kartách zadáním čísel ID článků znalostní báze. Při zadávání jednoho nebo více čísel ID je nutné odebrat nebo zrušit kontrolu všech klasifikací v nasazení aktualizace. Tím se zajistí, že balíček aktualizace nebude zahrnovat žádné další aktualizace při zadávání ID aktualizací.

> [!NOTE]
> Je důležité, abyste věděli, že vyloučení přepisují. Například pokud definujete pravidlo vyloučení `*`, Update Management nainstaluje žádné opravy ani balíčky, protože jsou všechny vyloučené. Vyloučené opravy se pořád na počítači zobrazují jako chybějící. U počítačů se systémem Linux, pokud zahrnete balíček, který má vyloučený závislý balíček, Update Management nenainstaluje hlavní balíček.

> [!NOTE]
> Aktualizace, které byly nahrazeny pro zahrnutí do nasazení aktualizace, nelze zadat.
>

* **Nastavení plánu:** Otevře se podokno Nastavení plánu. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. V části **Opakování** vyberte **Jednou**. Ponechte výchozí hodnotu 1 den a klikněte na **OK**. Tyto položky nastaví plán opakování.

* **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).

* **Časové období údržby (minuty):** Ponechte výchozí hodnotu. Časový interval pro správu a údržbu určuje dobu, po kterou je možné instalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti:

  * Časové intervaly pro správu a údržbu určují, kolik aktualizací se instaluje.
  * Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
  * Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového období údržby.
  * Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, je často k instalaci aktualizace Service Pack trvat delší dobu.

  > [!NOTE]
  > Pokud nechcete, aby se aktualizace používaly mimo časové období údržby v Ubuntu, překonfigurujte balíček bezobslužné aktualizace tak, aby nedocházelo k automatickým aktualizacím. Informace o tom, jak nakonfigurovat balíček, najdete [v tématu věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Možnosti restartování**: slouží k zadání možností pro zpracování restartování. K dispozici jsou následující možnosti:
  * V případě potřeby restartování (výchozí)
  * Vždy restartovat
  * Nikdy nerestartovat
  * Jenom restartovat – neinstaluje aktualizace

> [!NOTE]
> Klíče registru uvedené v části [klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) můžou způsobit restart události, pokud jsou **Možnosti restartování** nastavené na **Nikdy nerestartovat**.

Až skončíte s konfigurací plánu, klikněte na **vytvořit**.

![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vrátíte se na řídicí panel stavu. Vyberte **plánovaná nasazení aktualizací** , aby se zobrazil plán nasazení, který jste právě vytvořili.

> [!NOTE]
> Update Management podporuje nasazení aktualizací First stran a předběžného stahování oprav. Tato podpora vyžaduje změny v systémech, které jsou opraveny. Další informace o tom, jak nakonfigurovat tato nastavení v systémech, najdete v části [First a podpora předběžného stažení](automation-configure-windows-update.md) .

Nasazení aktualizací můžete také vytvořit programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou můžete použít k vytvoření týdenního nasazení aktualizací. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **úspěch**. Pokud dojde k chybám s jednou nebo více aktualizacemi v nasazení, stav je **částečně neúspěšný**.

Výběrem dokončeného nasazení aktualizace zobrazíte jeho řídicí panel.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.

Dostupné hodnoty jsou:

* **Nedošlo k pokusu**: aktualizace se nenainstalovala, protože na základě definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch**: aktualizace byla úspěšná.
* **Chyba**: aktualizace se nezdařila.

Výběrem **všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

Po úspěšném nasazení aktualizace obdržíte e-mail s potvrzením, který bude vypadat nějak takto:

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Update Management
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

Pokračujte k přehledu řešení Update Management.

> [!div class="nextstepaction"]
> [Řešení Update Management](automation-update-management.md)