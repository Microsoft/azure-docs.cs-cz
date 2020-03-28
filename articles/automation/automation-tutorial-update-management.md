---
title: Správa aktualizací a oprav pro virtuální počítače Azure
description: Tento článek obsahuje přehled o tom, jak pomocí Azure Automation Update Management ke správě aktualizací a oprav pro vaše virtuální počítače Azure a jiné než Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: f7130f3289ce42ca1481ec14be893c846c9ed55b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335792"
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

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Pokud jsou některé aktualizace označeny jako chybějící, zobrazí se na kartě **Chybějící aktualizace** seznam chybějících aktualizací.

V části **Odkaz Informace**vyberte odkaz na aktualizaci a otevřete článek podpory pro aktualizaci. Můžete se dozvědět důležité informace o aktualizaci.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknutím na libovolné jiné místo v aktualizaci otevřete podokno **hledání protokolu** pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Tento dotaz můžete upravit nebo vytvořit vlastní dotaz a zobrazit podrobné informace o nasazených nebo chybějících aktualizacích ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

V tomto kroku se naučíte nastavit výstrahu, abyste věděli o stavu nasazení aktualizace.

### <a name="alert-conditions"></a>Podmínky upozornění

V účtu Automation přejděte v části **Sledování** na **položku Výstrahy**a potom klikněte na **tlačítko + Nové pravidlo výstrah**.

Váš účet automatizace je již vybrán jako prostředek. Pokud ji chcete změnit, můžete kliknout na **Vybrat** a na stránce **Vybrat prostředek** vyberte **v** rozevíracím seznamu **Filtrovat podle typu prostředku.** Vyberte svůj účet Automation a pak vyberte **Hotovo**.

Klepnutím na tlačítko **Přidat podmínku** vyberte signál, který je vhodný pro nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o dvou dostupných signálech pro nasazení aktualizací:

|Název signálu|Dimenze|Popis|
|---|---|---|
|**Celkový počet spuštění nasazení aktualizace**|- Aktualizovat název nasazení</br>- Stav|Tento signál slouží k upozornění na celkový stav nasazení aktualizace.|
|**Celkový počet spuštění počítače pro nasazení aktualizace**|- Aktualizovat název nasazení</br>- Stav</br>- Cílový počítač</br>- Aktualizovat ID spuštění nasazení|Tento signál slouží k upozornění na stav nasazení aktualizace zaměřeného na konkrétní počítače.|

Pro hodnoty dimenzí vyberte platnou hodnotu ze seznamu. Pokud hledačná hodnota není v seznamu, klikněte **\+** na znaménko vedle dimenze a zadejte vlastní název. Poté můžete vybrat hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty z dimenze, klepněte na tlačítko **Vybrat. \* ** Pokud nezvolíte hodnotu dimenze, bude tato dimenze během hodnocení ignorována.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **Logika upozornění** jako **Prahová hodnota** zadejte **1**. Jakmile budete hotovi, vyberte **Hotovo**.

### <a name="alert-details"></a>Podrobnosti upozornění

Do **2. Definujte podrobnosti výstrahy**, zadejte název a popis výstrahy. Nastavte **Závažnost** na **Informativní (záv. 2)** pro úspěšné spuštění a **Informativní (záv. 1)** pro neúspěšné spuštění.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **Skupiny akcí**vyberte **Vytvořit nový**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

Do pole **Název skupiny akcí** zadejte název a krátký název upozornění. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

V části **Akce**zadejte název akce, například **E-mailová oznámení**. V části **Typ akce**vyberte **Možnost E-mail/SMS/Push/Voice**. V části **Podrobnosti**vyberte **Upravit podrobnosti**.

V podokně **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

V podokně **E-mail/SMS/Push/Hlas** vyberte **ok**. V podokně **Přidat skupinu akcí** vyberte **Ok**.

Chcete-li přizpůsobit předmět e-mailu s výstrahami, vyberte v části **Vytvořit pravidlo**v části **Přizpůsobit akce** **předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Pravidlo vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Dále naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro instalaci aktualizací. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

>[!NOTE]
>Při plánování nasazení aktualizace vytvoří [plán](shared-resources/schedules.md) ový prostředek propojený se souborem runbook **Patch-MicrosoftOMSComputers,** který zpracovává nasazení aktualizace v cílových počítačích. Pokud odstraníte prostředek plánu z webu Azure portal nebo pomocí PowerShellu po vytvoření nasazení, přeruší plánované nasazení aktualizace a zobrazí chybu při pokusu o jeho překonfigurali z portálu. Zdroj plánu lze odstranit pouze odstraněním odpovídajícího plánu nasazení.  
>

Pokud chcete pro virtuální počítač naplánovat nové nasazení aktualizací, přejděte do části **Správa aktualizací** a pak vyberte **Naplánovat nasazení aktualizací**.

V části **Nové nasazení aktualizací** zadejte následující informace:

* **Název:** Zadejte jedinečný název nasazení aktualizací.

* **Operační systém:** Vyberte cílový operační systém pro nasazení aktualizací.

* **Skupiny, které se mají aktualizovat (Preview)**: Definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek a vytvořte dynamickou skupinu virtuálních počítačů Azure, která se má zahrnout do vašeho nasazení. Další informace najdete v [tématu Dynamické skupiny](automation-update-management-groups.md)

* **Počítače k aktualizaci:** Vyberte uložené hledání, importovanou skupinu nebo vyberte jednotlivé počítače z rozevírací nabídky. Pokud zvolíte **stroje**, připravenost stroje se zobrazí ve sloupci **Připravenost agenta aktualizace.** Informace o různých způsobech vytváření skupin počítačů v protokolech Azure Monitoru najdete [v tématu Skupiny počítačů v protokolech Azure Monitoru.](../azure-monitor/platform/computer-groups.md)

* **Klasifikace aktualizací**: Vyberte podporované klasifikace aktualizací, které jsou k dispozici pro každý produkt, který lze zahrnout do nasazení aktualizace. Pro účely tohoto kurzu nechte vybrané všechny typy.

  Typy klasifikace jsou:

   |Operační systém  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popis typů klasifikace najdete v tématu popisujícím [klasifikace aktualizací](automation-view-update-assessments.md#update-classifications).

* **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách.

> [!NOTE]
> Je důležité vědět, že vyloučení přepsat inkluze. Pokud například definujete pravidlo `*`vyloučení aplikace , nebudou nainstalovány žádné opravy ani balíčky, protože jsou všechny vyloučeny. Vyloučené záplaty se stále zobrazují jako chybějící ze stroje. Pro linuxové počítače, pokud je balíček součástí balení, ale má závislý balíček, který byl vyloučen, balíček není nainstalován.

> [!NOTE]
> Nelze zadat aktualizace, které byly nahrazeny pro zahrnutí s nasazením aktualizace.
>

* **Nastavení plánu:** Otevře se podokno **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. V části **Opakování** vyberte **Jednou**. Ponechte výchozí hodnotu jako 1 den a vyberte **ok**. Tím se nastaví opakovaný plán.

* **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).

* **Časové období údržby (minuty):** Ponechte výchozí hodnotu. Okna údržby řídí dobu povolenou pro instalaci aktualizací. Při zadávání okna údržby zvažte následující podrobnosti.

  * Systém Windows pro údržbu řídí, kolik aktualizací se pokouší tena o instalaci.
  * Správa aktualizací nepřestane instalovat nové aktualizace, pokud se blíží konec okna údržby.
  * Správa aktualizací neukončí probíhající aktualizace, pokud je překročeno okno údržby.
  * Pokud je v systému Windows překročeno okno údržby, je to často z důvodu instalace aktualizace aktualizace Service Pack, která trvá dlouhou dobu.

  > [!NOTE]
  > Chcete-li zabránit aktualizaci se používá mimo okno údržby na Ubuntu, překonfigurovat bezobslužný upgrade balíček zakázat automatické aktualizace. Informace o konfiguraci balíčku naleznete [v tématu Automatické aktualizace v Průvodci serverem Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Možnosti restartování:** Toto nastavení určuje, jak se má provádět restartování. Dostupné možnosti jsou:
  * Restartovat v případě potřeby (výchozí)
  * Vždy restartovat
  * Nikdy nerestartovat
  * Pouze restartovat – nenainstalují se aktualizace

> [!NOTE]
> Klíče registru uvedené v části [Klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mohou způsobit událost restartování, pokud je funkce Řízení **restartování** nastavena na možnost Nikdy **se nerestartovat**.

Jakmile budete s konfigurací plánu hotovi, vyberte **Vytvořit**.

![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vrátíte se na řídicí panel stavu. Vyberte **Naplánovaná nasazení aktualizací** a zobrazte plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje určité změny v opravovaných systémech. Informace o konfiguraci těchto nastavení pro vaše systémy najdete v části věnované [podpoře aktualizací od Microsoftu a předběžného stahování](automation-configure-windows-update.md).

**Aktualizace nasazení** lze také vytvořit programově. Informace o tom, jak vytvořit **nasazení aktualizace** pomocí rozhraní REST API, naleznete v [tématu Konfigurace aktualizací softwaru – vytvoření](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také ukázkový runbook, který lze použít k vytvoření **týdenního nasazení aktualizace**. Další informace o tomto runbooku najdete [v tématu Vytvoření týdenní aktualizace nasazení pro jeden nebo více virtuálních počítače ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Úspěch**. Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.

Výběrem dokončeného nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.

Následující seznam ukazuje dostupné hodnoty:

* **Nebyl proveden pokus:** Aktualizace se nenainstalovala, protože podle definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěšné**: Aktualizace byla úspěšná.
* **Nezdařilo**se: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

Po úspěšném nasazení aktualizací se odešle podobný e-mail jako v následujícím příkladu, který značí úspěch nasazení:

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

