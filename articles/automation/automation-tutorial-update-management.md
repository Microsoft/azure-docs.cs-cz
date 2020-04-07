---
title: Správa aktualizací a oprav pro virtuální počítače Azure
description: Tento článek obsahuje přehled o tom, jak pomocí Azure Automation Update Management ke správě aktualizací a oprav pro vaše virtuální počítače Azure a jiné než Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 888dc99162551482afc715f1a793614d2c866384
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677038"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Správa aktualizací a oprav pro virtuální počítače Azure

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače. V tomto kurzu se naučíte rychle vyhodnotit stav dostupných aktualizací, plánovat instalaci požadovaných aktualizací, kontrolovat výsledky nasazení a vytvořit upozornění za účelem ověření správného použití aktualizací.

Informace o cenách naleznete v [tématu Automation pricing for Update Management](https://azure.microsoft.com/pricing/details/automation/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Řešení [správy aktualizací](automation-update-management.md) povolené pro jeden nebo více virtuálních počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení správy aktualizací se otevře stránka **Správa aktualizací.** Pokud jsou některé aktualizace označeny jako chybějící, zobrazí se na kartě **Chybějící aktualizace** seznam chybějících aktualizací.

V části **Odkaz Informace**vyberte odkaz na aktualizaci a otevřete článek podpory pro aktualizaci. Můžete se dozvědět důležité informace o aktualizaci.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknutím na libovolné jiné místo v aktualizaci otevřete podokno **hledání protokolu** pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Tento dotaz můžete upravit nebo vytvořit vlastní dotaz a zobrazit podrobné informace o aktualizacích nasazených nebo chybějících ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

V tomto kroku se naučíte nastavit výstrahu, abyste věděli o stavu nasazení aktualizace.

### <a name="alert-conditions"></a>Podmínky upozornění

V účtu Automation přejděte v části **Výstrahy** v části **Sledování**a klikněte na **nové pravidlo výstrah**.

Váš účet automatizace je již vybrán jako prostředek. Pokud ji chcete změnit, klepněte na **tlačítko Vybrat**. Na stránce **Vybrat prostředek** zvolte **Automatizační účty** z rozevírací nabídky Filtr podle typu **prostředku.** Vyberte účet Automation a klepněte na tlačítko **Hotovo**.

Klepnutím na tlačítko **Přidat podmínku** vyberte signál, který je vhodný pro nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o dvou dostupných signálech.

|Název signálu|Dimenze|Popis|
|---|---|---|
|`Total Update Deployment Runs`|- Aktualizovat název nasazení<br>- Stav|Tento signál upozorňuje na celkový stav nasazení aktualizace.|
|`Total Update Deployment Machine Runs`|- Aktualizovat název nasazení</br>- Stav</br>- Cílový počítač</br>- Aktualizovat ID spuštění nasazení|Tento signál upozorňuje na stav nasazení aktualizace zaměřené na konkrétní počítače.|

Pro dimenzi vyberte platnou hodnotu ze seznamu. Pokud požadovaná hodnota není v seznamu, **\+** klikněte na znaménko vedle dimenze a zadejte vlastní název. Pak vyberte hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty pro dimenzi, klepněte na tlačítko **Vybrat. \* ** Pokud pro dimenzi nezvolíte hodnotu, správa aktualizací tuto dimenzi ignoruje.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **Logika upozornění** jako **Prahová hodnota** zadejte **1**. Jakmile budete hotovi, vyberte **Hotovo**.

### <a name="alert-details"></a>Podrobnosti upozornění

Do **2. Definujte podrobnosti výstrahy**, zadejte název a popis výstrahy. Nastavte **Závažnost** na **Informativní (záv. 2)** pro úspěšné spuštění a **Informativní (záv. 1)** pro neúspěšné spuštění.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **Skupiny akcí**vyberte **Vytvořit nový**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Akce mohou zahrnovat e-mailová oznámení, runbooky, webhooky a mnoho dalších. Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

Do pole **Název skupiny akcí** zadejte název výstrahy a krátký název. Správa aktualizací používá krátký název místo úplného názvu skupiny akcí při odesílání oznámení pomocí zadané skupiny.

V části **Akce**zadejte název akce, například **E-mailové oznámení**. V **případě typu akce**vyberte možnost **E-mail/SMS/Push/Voice**. V **části Podrobnosti**vyberte **možnost Upravit podrobnosti**.

V podokně **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

V podokně **E-mail/SMS/Push/Hlas** klepněte na **tlačítko OK**. V podokně **Přidat skupinu akcí** klepněte na tlačítko **OK**.

Chcete-li přizpůsobit předmět e-mailu s výstrahami, vyberte v části **Vytvořit pravidlo**v části **Přizpůsobit akce** **předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Výstraha vás upozorní, když je nasazení aktualizace úspěšné a které počítače byly součástí spuštění nasazení aktualizace.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Dále naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro instalaci aktualizací. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Plánování nasazení aktualizace vytvoří [zdroj plánu](shared-resources/schedules.md) propojený se souborem Runbook **Patch-MicrosoftOMSComputers,** který zpracovává nasazení aktualizace v cílových počítačích. Pokud odstraníte prostředek plánu z webu Azure portal nebo pomocí PowerShellu po vytvoření nasazení, odstranění přeruší plánované nasazení aktualizace a zobrazí chybu při pokusu o překonfigurovat prostředek plánu z portálu. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  

Pokud chcete pro virtuální počítač naplánovat nové nasazení aktualizací, přejděte do části **Správa aktualizací** a pak vyberte **Naplánovat nasazení aktualizací**.

V části **Nové nasazení aktualizací** zadejte následující informace:

* **Název:** Zadejte jedinečný název nasazení aktualizací.

* **Operační systém:** Vyberte cílový operační systém pro nasazení aktualizací.

* **Skupiny k aktualizaci (preview):** Definujte dotaz, který kombinuje předplatné, skupiny prostředků, umístění a značky k vytvoření dynamické skupiny virtuálních počítačích Azure, které chcete zahrnout do vašeho nasazení. Další informace naleznete v [tématu Dynamické skupiny](automation-update-management-groups.md).

* **Počítače k aktualizaci**: Vyberte uložené hledání, Importované skupiny nebo vyberte **stroje** z rozbalovací nabídky a vyberte jednotlivé počítače. Pokud zvolíte **stroje**, připravenost každého počítače se zobrazí ve sloupci **Připravenost agenta aktualizace.** Informace o různých způsobech vytváření skupin počítačů v protokolech Azure Monitoru najdete [v tématu Skupiny počítačů v protokolech Azure Monitor .](../azure-monitor/platform/computer-groups.md)

* **Klasifikace aktualizací**: Pro každý produkt zrušte výběr všech podporovaných klasifikací aktualizací, ale ty, které chcete zahrnout do nasazení aktualizace. V tomto kurzu ponechte všechny typy vybrané pro všechny produkty.

  Typy klasifikace jsou:

   |Operační systém  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace<br>Ovladač        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popis typů klasifikace naleznete v tématu [Aktualizace klasifikací](automation-view-update-assessments.md#update-classifications).

* **Aktualizace zahrnout/vyloučit** - Otevře stránku Zahrnout nebo vyloučit. Aktualizace, které mají být zahrnuty nebo vyloučeny, jsou na samostatných kartách zadáním čísel ID článku KB. Při zadávání jednoho nebo více čísel ID je třeba odebrat nebo zrušit kontrolu všech klasifikací s nasazením aktualizace. Tím zajistíte, že žádné další aktualizace jsou zahrnuty v balíčku aktualizace při zadávání ID aktualizace.

> [!NOTE]
> Je důležité vědět, že vyloučení přepsat inkluze. Pokud například definujete pravidlo `*`vyloučení aplikace , správa aktualizací nenainstaluje žádné opravy ani balíčky, protože jsou všechny vyloučeny. Vyloučené záplaty se stále zobrazují jako chybějící ze stroje. Pro počítače s Linuxem, pokud zahrnete balíček, který má závislý balíček, který byl vyloučen, správa aktualizací nenainstaluje hlavní balíček.

> [!NOTE]
> Nelze zadat aktualizace, které byly nahrazeny pro zahrnutí s nasazením aktualizace.
>

* **Nastavení plánu:** Otevře se podokno **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. V části **Opakování** vyberte **Jednou**. Ponechejte výchozí hodnotu jako 1 den a klepněte na tlačítko **OK**. Tyto položky nastavují opakovaný plán.

* **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).

* **Časové období údržby (minuty):** Ponechte výchozí hodnotu. Okna údržby řídí dobu povolenou pro instalaci aktualizací. Při zadávání okna údržby zvažte následující podrobnosti:

  * Systém Windows pro údržbu řídí, kolik aktualizací je nainstalováno.
  * Správa aktualizací nepřestane instalovat nové aktualizace, pokud se blíží konec okna údržby.
  * Správa aktualizací neukončí probíhající aktualizace, pokud je překročeno okno údržby.
  * Pokud je v systému Windows překročeno okno údržby, je to často proto, že instalace aktualizace aktualizace Service Pack trvá dlouho.

  > [!NOTE]
  > Chcete-li zabránit aktualizaci se používá mimo okno údržby na Ubuntu, překonfigurovat bezobslužný upgrade balíček zakázat automatické aktualizace. Informace o konfiguraci balíčku naleznete [v tématu Automatické aktualizace v Průvodci serverem Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Možnosti restartování**: Slouží k určení možností pro zpracování restartování. K dispozici jsou následující možnosti:
  * V případě potřeby restartujte počítač (výchozí)
  * Vždy restartovat
  * Nikdy nerestartovat
  * Pouze restart - neinstaluje aktualizace

> [!NOTE]
> Klíče registru uvedené v části [Klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mohou způsobit událost restartování, pokud je možnost **restartování** nastavena na možnost Nikdy **se nerestartovat**.

Po dokončení konfigurace plánu klepněte na tlačítko **Vytvořit**.

![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vrátíte se na řídicí panel stavu. Vyberte **Nasazení naplánované aktualizace,** chcete-li zobrazit právě vytvořený plán nasazení.

> [!NOTE]
> Správa aktualizací podporuje nasazení aktualizací první strany a opravy předběžného stažení. Tato podpora vyžaduje změny v systémech, které jsou opravy. Informace o konfiguraci těchto nastavení v systémech najdete v článku [První strana a podpora před stažením.](automation-configure-windows-update.md)

Můžete také vytvořit nasazení aktualizací programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí rozhraní REST API, naleznete v [tématu Konfigurace aktualizací softwaru – vytvoření](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také ukázkový runbook, který můžete použít k vytvoření týdenní aktualizace nasazení. Další informace o tomto runbooku najdete [v tématu Vytvoření týdenní aktualizace nasazení pro jeden nebo více virtuálních počítače ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Succeeded**. Pokud dojde k chybám s jednou nebo více aktualizacemi v nasazení, stav se **částečně nezdařil**.

Vyberte dokončené nasazení aktualizace, chcete-li zobrazit jeho řídicí panel.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.

Dostupné hodnoty jsou:

* **Nepokusil:** Aktualizace nebyla nainstalována, protože nebyl k dispozici dostatek času, na základě definované doby trvání okna údržby.
* **Úspěšné**: Aktualizace byla úspěšná.
* **Nezdařilo**se: Aktualizace se nezdařila.

Výběrem **možnosti Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

Po úspěšném nasazení aktualizace obdržíte potvrzující e-mail podobný následujícímu:

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