---
title: Správa aktualizací a oprav pro virtuální počítače Azure
description: Tento článek poskytuje přehled použití řešení Update Management služby Azure Automation ke správě aktualizací a oprav pro vaše virtuální počítače Azure s Windows.
services: automation
author: zjalexander
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: a2d13833b60076caa371a7fa8a696ab5964a28e3
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376083"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Správa aktualizací a oprav pro virtuální počítače Azure

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače. V tomto kurzu se naučíte rychle vyhodnotit stav dostupných aktualizací, plánovat instalaci požadovaných aktualizací, kontrolovat výsledky nasazení a vytvořit upozornění za účelem ověření správného použití aktualizací.

Informace o cenách najdete na stránce s [cenami služby Automation za řešení Update Management](https://azure.microsoft.com/pricing/details/automation/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Update Management
> * Zobrazení posouzení aktualizací
> * Konfigurace upozorňování
> * Naplánování nasazení aktualizace
> * Zobrazení výsledků nasazení

## <a name="prerequisites"></a>Předpoklady

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

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management. Toto ověření zahrnuje kontroly pracovního prostoru služby Azure Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení Update Management nachází v tomto pracovním prostoru.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Microsoft Monitoring Agent (MMA) a funkcí Hybrid Runbook Worker služby Automation. Agent slouží ke komunikaci se službou Azure Automation a získávání informací o stavu aktualizací. Agent ke komunikaci se službou Azure Automation a stahování aktualizací vyžaduje otevřený port 443.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Účet Automation](./automation-offering-get-started.md)
* Funkce [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (povolená na virtuálním počítači)

V části **Update Management** nastavte umístění, pracovní prostor služby Log Analytics a účet Automation, které se mají použít. Pak vyberte **Povolit**. Pokud tyto možnosti nejsou dostupné, znamená to, že pro virtuální počítač je povolené jiné řešení automatizace. V takovém případě se musí použít stejný pracovní prostor a účet Automation.

![Okno Povolit řešení Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Povolení řešení může trvat až několik minut. Během této doby nezavírejte okno prohlížeče. Po povolení řešení budou informace o chybějících aktualizacích na virtuálním počítači Azure Monitor protokoly. Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Pokud nějaké aktualizace chybí, na kartě **Chybějící aktualizace** se zobrazí seznam chybějících aktualizací.

V části **ODKAZ NA INFORMACE** vyberte odkaz na nějakou aktualizaci a v novém okně se otevře článek podpory o dané aktualizaci. V tomto okně najdete důležité informace týkající se dané aktualizace.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknutím kamkoli jinam na aktualizaci otevřete podokno **Prohledávání protokolu** pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Tento dotaz můžete upravit nebo vytvořit vlastní dotaz a zobrazit podrobné informace o nasazených nebo chybějících aktualizacích ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

V tomto kroku se naučíte nastavit výstrahu, která vám umožní zjistit stav nasazení aktualizace.

### <a name="alert-conditions"></a>Podmínky upozornění

V účtu Automation v části **monitorování** přejděte na **výstrahy**a pak klikněte na **+ nové pravidlo výstrahy**.

Váš účet Automation už je vybraný jako prostředek. Pokud ho chcete změnit, můžete kliknout na **Vybrat** a na stránce **Vybrat prostředek** vybrat **účty Automation** v rozevíracím seznamu **filtrovat podle typu prostředku** . Vyberte svůj účet Automation a pak vyberte **Hotovo**.

Klikněte na **Přidat podmínku** a vyberte signál, který je vhodný pro nasazení aktualizace. V následující tabulce jsou uvedeny podrobnosti o dvou dostupných signálech pro nasazení aktualizací:

|Název signálu|Dimenze|Popis|
|---|---|---|
|**Celkový počet spuštění nasazení aktualizací**|– Název nasazení aktualizace</br>-Status|Tento signál slouží k upozornění na celkový stav nasazení aktualizace.|
|**Celkový počet spuštěných počítačů nasazení aktualizace**|– Název nasazení aktualizace</br>-Status</br>– Cílový počítač</br>– Aktualizovat ID běhu nasazení|Tento signál se používá k upozornění na stav nasazení aktualizace zaměřeného na konkrétní počítače.|

V poli hodnoty dimenze vyberte platnou hodnotu ze seznamu. Pokud hodnota, kterou hledáte, není v seznamu, klikněte na symbol **\+** vedle dimenze a zadejte vlastní název. Pak můžete vybrat hodnotu, kterou chcete vyhledat. Pokud chcete vybrat všechny hodnoty z dimenze, klikněte na tlačítko **vybrat \*** . Pokud nevyberete hodnotu pro dimenzi, tato dimenze se během hodnocení ignoruje.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **Logika upozornění** jako **Prahová hodnota** zadejte **1**. Jakmile budete hotovi, vyberte **Hotovo**.

### <a name="alert-details"></a>Podrobnosti upozornění

V části **2. Definujte podrobnosti výstrahy**, zadejte název a popis výstrahy. Nastavte **Závažnost** na **Informativní (záv. 2)** pro úspěšné spuštění a **Informativní (záv. 1)** pro neúspěšné spuštění.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **skupiny akcí**vyberte **vytvořit novou**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

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

* **Název:** Zadejte jedinečný název nasazení aktualizací.

* **Operační systém:** Vyberte cílový operační systém pro nasazení aktualizací.

* **Skupiny, které se mají aktualizovat (Preview)** : Definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek a vytvořte dynamickou skupinu virtuálních počítačů Azure, která se má zahrnout do vašeho nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management-groups.md) .

* **Počítače k aktualizaci:** Vyberte uložené hledání, importovanou skupinu nebo vyberte jednotlivé počítače z rozevírací nabídky. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů. Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md)

* **Klasifikace aktualizací:** Vyberte typy softwaru, které se zahrnou do nasazení aktualizací. Pro účely tohoto kurzu nechte vybrané všechny typy.

  Typy klasifikace jsou:

   |OS  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popis typů klasifikace najdete v tématu popisujícím [klasifikace aktualizací](automation-view-update-assessments.md#update-classifications).

* **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách.

> [!NOTE]
> Je důležité, abyste věděli, že vyloučení přepisují. Například pokud definujete pravidlo vyloučení `*`, nebudou nainstalovány žádné opravy ani balíčky, protože všechny jsou vyloučené. Vyloučené opravy se pořád na počítači zobrazují jako chybějící. Pro počítače se systémem Linux, pokud je zahrnut balíček, ale obsahuje závislý balíček, který byl vyloučen, není balíček nainstalován.

* **Nastavení plánu:** Otevře se podokno **Nastavení plánu**. Výchozí čas spuštění je 30 minut po aktuálním čase. Čas spuštění můžete nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. V části **Opakování** vyberte **Jednou**. Ponechte výchozí hodnotu 1 den a vyberte **OK**. Tím se nastaví opakovaný plán.

* **Předzálohovací a pozálohovací skripty**: Vyberte skripty, které se mají spustit před vaším nasazením a po něm. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).

* **Časové období údržby (minuty):** Ponechte výchozí hodnotu. Časový interval pro správu a údržbu určuje dobu, po kterou je možné instalovat aktualizace. Při určování časového období údržby Vezměte v úvahu následující podrobnosti.

  * Časové intervaly pro správu a údržbu určují, kolik aktualizací se zkouší nainstalovat.
  * Update Management neukončí instalaci nových aktualizací, pokud se blíží konec okna údržby.
  * Update Management neukončí probíhající aktualizace, pokud dojde k překročení časového intervalu pro správu a údržbu.
  * Pokud dojde k překročení časového intervalu pro správu a údržbu v systému Windows, často je instalace aktualizace Service Pack delší dobu trvat.

  > [!NOTE]
  > "Tím, že" informace nejsou důležité pro taskTo, aby se aktualizace nepoužívaly mimo časové období údržby v Ubuntu, překonfigurujte balíček bezobslužné aktualizace tak, aby nedocházelo k automatickým aktualizacím. Informace o tom, jak nakonfigurovat balíček, najdete [v tématu věnovaném automatickým aktualizacím v příručce k serveru Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Možnosti restartování:** Toto nastavení určuje, jak se má provádět restartování. Dostupné možnosti jsou:
  * Restartovat v případě potřeby (výchozí)
  * Vždy restartovat
  * Nikdy nerestartovat
  * Pouze restartovat – nenainstalují se aktualizace

> [!NOTE]
> Klíče registru uvedené v části [klíče registru používané ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mohou způsobit událost restartování, pokud je **ovládací prvek restart** nastaven na možnost **Nikdy nerestartovat**.

Jakmile budete s konfigurací plánu hotovi, vyberte **Vytvořit**.

![Podokno nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vrátíte se na řídicí panel stavu. Vyberte **Naplánovaná nasazení aktualizací** a zobrazte plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje určité změny v opravovaných systémech. Informace o konfiguraci těchto nastavení pro vaše systémy najdete v části věnované [podpoře aktualizací od Microsoftu a předběžného stahování](automation-configure-windows-update.md).

**Nasazení aktualizací** lze také vytvořit programově. Informace o tom, jak vytvořit **nasazení aktualizace** pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou lze použít k vytvoření týdenního **nasazení aktualizací**. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**. Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Úspěch**. Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.

Výběrem dokončeného nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazení aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

V části **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.

Následující seznam ukazuje dostupné hodnoty:

* **Nebyl proveden pokus:** Aktualizace se nenainstalovala, protože podle definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch:** Aktualizace byla úspěšná.
* **Neúspěch:** Aktualizace se nezdařila.

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
> [Řešení Update Management](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

