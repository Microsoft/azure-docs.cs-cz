---
title: Správa aktualizací a oprav pro virtuální počítače Azure s Windows
description: Tento článek poskytuje přehled použití řešení Update Management služby Azure Automation ke správě aktualizací a oprav pro vaše virtuální počítače Azure s Windows.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 83647dfb0965b8aac8ede5f2e9669ae3d7722c41
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184980"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Správa aktualizací pro Windows pomocí služby Azure Automation

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače. V tomto kurzu se naučíte rychle vyhodnotit stav dostupných aktualizací, plánovat instalaci požadovaných aktualizací, kontrolovat výsledky nasazení a vytvořit upozornění za účelem ověření správného použití aktualizací.

Informace o cenách najdete na stránce s [cenami služby Automation za řešení Update Management](https://azure.microsoft.com/pricing/details/automation/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Update Management
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ho ještě nemáte, můžete si [aktivovat měsíční kredit Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Azure Automation](automation-offering-get-started.md), který bude obsahovat sledovací proces, runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-update-management"></a>Povolení řešení Update Management

Pro účely tohoto kurzu nejprve na svém virtuálním počítači povolte řešení Update Management:

1. Na webu Azure Portal v levé nabídce vyberte **Virtuální počítače**. V seznamu vyberte virtuální počítač.
2. Na stránce virtuálního počítače v části **OPERACE** vyberte **Správa aktualizací**. Otevře se podokno **Povolit řešení Update Management**.

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management. Toto ověření zahrnuje kontroly pracovního prostoru Azure Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení Update Management nachází v tomto pracovním prostoru.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Microsoft Monitoring Agent (MMA) a funkcí Hybrid Runbook Worker služby Automation. Agent slouží ke komunikaci se službou Azure Automation a získávání informací o stavu aktualizací. Agent ke komunikaci se službou Azure Automation a stahování aktualizací vyžaduje otevřený port 443.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Účet Automation](./automation-offering-get-started.md)
* Funkce [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (povolená na virtuálním počítači)

V části **Update Management** nastavte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít. Pak vyberte **Povolit**. Pokud tyto možnosti nejsou dostupné, znamená to, že pro virtuální počítač je povolené jiné řešení automatizace. V takovém případě se musí použít stejný pracovní prostor a účet Automation.

![Okno Povolit řešení Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Povolení řešení může trvat až několik minut. Během této doby nezavírejte okno prohlížeče. Po povolení řešení začnou do Log Analytics proudit informace o chybějících aktualizacích na virtuálních počítačích. Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Pokud nějaké aktualizace chybí, na kartě **Chybějící aktualizace** se zobrazí seznam chybějících aktualizací.

V části **ODKAZ NA INFORMACE** vyberte odkaz na nějakou aktualizaci a v novém okně se otevře článek podpory o dané aktualizaci. V tomto okně najdete důležité informace týkající se dané aktualizace.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknutím kamkoli jinam na aktualizaci otevřete podokno **Prohledávání protokolu** pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Tento dotaz můžete upravit nebo vytvořit vlastní dotaz a zobrazit podrobné informace o nasazených nebo chybějících aktualizacích ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

V tomto kroku zjistíte, jak nastavit upozornění, abyste mohli zjistit stav nasazení aktualizace.

### <a name="alert-conditions"></a>Podmínky upozornění

Ve vašem účtu Automation v části **monitorování** přejděte na **výstrahy**a potom klikněte na tlačítko **+ nové pravidlo upozornění**.

Váš účet Automation je už vybraná jako prostředek. Pokud chcete změnit ji můžete kliknout na **vyberte** a na **vyberte prostředek** stránce **účty Automation** v **filtrovat podle typu prostředku** rozevíracího seznamu. Vyberte svůj účet Automation a pak vyberte **Hotovo**.

Klikněte na tlačítko **přidat podmínku** vyberte signál, který je vhodný pro vaše nasazení aktualizace. Následující tabulka zobrazuje podrobnosti o dvě dostupné signály pro nasazení aktualizací:

|Název signálu|Dimenze|Popis|
|---|---|---|
|**Celkový počet aktualizací je spuštěné nasazení**|-Název nasazení aktualizace</br>-Status|Tento signál slouží k upozornění na celkový stav nasazení aktualizace.|
|**Celkový počet aktualizací spuštění počítače nasazení**|-Název nasazení aktualizace</br>-Status</br>-Cílového počítače</br>– Id spuštění aktualizace nasazení|Tento signál slouží k upozornění na stav nasazení aktualizace cílená na konkrétní počítače|

Pro hodnoty rozměrů vyberte platnou hodnotu ze seznamu. Pokud hledáte hodnota není v seznamu, klikněte na tlačítko **\+** znaménko vedle dimenze a zadejte vlastní název. Pak můžete vybrat hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty z dimenze, klikněte na tlačítko **vyberte \***  tlačítko. Pokud jste nezvolili hodnotu pro dimenzi, daná dimenze se budou ignorovat během vyhodnocení.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **Logika upozornění** jako **Prahová hodnota** zadejte **1**. Jakmile budete hotovi, vyberte **Hotovo**.

### <a name="alert-details"></a>Podrobnosti upozornění

V části **2. Definujte podrobnosti upozornění** zadejte název a popis upozornění. Nastavte **Závažnost** na **Informativní (záv. 2)** pro úspěšné spuštění a **Informativní (záv. 1)** pro neúspěšné spuštění.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **skupiny akcí**vyberte **vytvořit nový**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

Do pole **Název skupiny akcí** zadejte název a krátký název upozornění. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

V části **Akce** zadejte název akce, například **E-mailová oznámení**. V části **TYP AKCE** vyberte **E-mailové/SMS/nabízené/hlasové oznámení**. V části **PODROBNOSTI** vyberte **Upravit podrobnosti**.

V podokně **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

V podokně **E-mailové/SMS/nabízené/hlasové oznámení** vyberte **OK**. V podokně **Přidat skupinu akcí** vyberte **OK**.

Pokud chcete upravit předmět e-mailového oznámení, na stránce **Vytvořit pravidlo** v části **Přizpůsobit akce** vyberte **Předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Pravidlo vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Dále naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro instalaci aktualizací. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Pokud chcete pro virtuální počítač naplánovat nové nasazení aktualizací, přejděte do části **Správa aktualizací** a pak vyberte **Naplánovat nasazení aktualizací**.

V části **Nové nasazení aktualizací** zadejte následující informace:

* **Název**: Zadejte jedinečný název pro nasazení aktualizace.

* **Operační systém**: Vyberte operační systém k cíli pro nasazení aktualizace.

* **Skupiny, které se aktualizace (preview)**: Definování dotazu na základě kombinace předplatného, skupiny prostředků, míst a značky vytvářet dynamické skupiny virtuálních počítačů Azure má zahrnout do vašeho nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management.md#using-dynamic-groups)

* **Počítače k aktualizaci**: Vyberte uložená hledání, importované skupiny, nebo vybrat počítač z rozevíracího seznamu a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů. Další informace o různých způsobech vytváření skupin počítačů v Log Analytics najdete v tématu [Skupiny počítačů v Log Analytics](../azure-monitor/platform/computer-groups.md).

* **Klasifikace aktualizací**: Vyberte typy softwaru, který nasazení aktualizací zahrnutých v nasazení. Pro účely tohoto kurzu nechte vybrané všechny typy.

  Typy klasifikace jsou:

   |Operační systém  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popis typů klasifikace najdete v tématu popisujícím [klasifikace aktualizací](automation-update-management.md#update-classifications).

* **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o zpracování zařazení, naleznete v tématu [zahrnutí chování](automation-update-management.md#inclusion-behavior)

* **Nastavení plánu**: **Nastavení plánu** se otevře podokno. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. V části **Opakování** vyberte **Jednou**. Ponechte výchozí hodnotu 1 den a vyberte **OK**. Tím se nastaví opakovaný plán.

* **Skripty před a po skripty**: Vyberte skripty spouštěné před a po nasazení. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).
* **Časové období údržby (minuty)**: Nechte zadanou výchozí hodnotu. Můžete nastavit časové okno, ve kterém má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

* **Možnosti restartování**: Toto nastavení určuje, jak by měl být zpracována restartování počítače. Dostupné možnosti jsou:
  * Restartovat v případě potřeby (výchozí)
  * Vždy restartovat
  * Nikdy nerestartovat
  * Pouze restartovat – nenainstalují se aktualizace

Jakmile budete s konfigurací plánu hotovi, vyberte **Vytvořit**.

![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vrátíte se na řídicí panel stavu. Vyberte **Naplánovaná nasazení aktualizací** a zobrazte plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje určité změny v opravovaných systémech. Informace o konfiguraci těchto nastavení pro vaše systémy najdete v části věnované [podpoře aktualizací od Microsoftu a předběžného stahování](automation-update-management.md#firstparty-predownload).

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Úspěch**. Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.

Výběrem dokončeného nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.

Následující seznam ukazuje dostupné hodnoty:

* **Nebyl proveden pokus**: Aktualizace se nenainstalovala, protože nebylo k dispozici dostatek času podle definovaného trvání okna údržby.
* **Úspěšné**: Aktualizace byla úspěšná.
* **Nepovedlo**: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

Po úspěšném nasazení aktualizací se odešle podobný e-mail jako v následujícím příkladu, který značí úspěch nasazení:

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Update Management
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

Pokračujte k přehledu řešení Update Management.

> [!div class="nextstepaction"]
> [Řešení Update Management](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
