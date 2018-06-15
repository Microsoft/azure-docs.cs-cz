---
title: Správa aktualizací a oprav pro virtuální počítače Azure s Windows
description: Tento článek poskytuje přehled použití řešení Update Management služby Azure Automation ke správě aktualizací a oprav pro vaše virtuální počítače Azure s Windows.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054755"
---
# <a name="manage-windows-updates-with-azure-automation"></a>Správa aktualizací pro Windows pomocí služby Azure Automation

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače.
V tomto kurzu se naučíte rychle vyhodnotit stav dostupných aktualizací, plánovat instalaci požadovaných aktualizací, kontrolovat výsledky nasazení a vytvořit upozornění za účelem ověření správného použití aktualizací.

Informace o cenách najdete na stránce s [cenami služby Automation za správu aktualizací](https://azure.microsoft.com/pricing/details/automation/).

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
* [Účet Automation](automation-offering-get-started.md), který bude obsahovat sledovací proces, runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-update-management"></a>Povolení řešení Update Management

Pro účely tohoto kurzu je potřeba nejprve na virtuálním počítači povolit řešení Update Management.

1. Na webu Azure Portal v nabídce vlevo vyberte **Virtuální počítače** a ze seznamu vyberte virtuální počítač.
2. Na stránce virtuálního počítače v části **Operace** klikněte na **Update Management**. Otevře se stránka **Povolit řešení Update Management**.

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management. Toto ověření zahrnuje kontroly pracovního prostoru Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení Update Management nachází v tomto pracovním prostoru.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Microsoft Monitoring Agent (MMA) a hybridním pracovním procesem runbooku Automation.
Agent slouží ke komunikaci se službou Azure Automation a získávání informací o stavu aktualizací. Agent ke komunikaci se službou Azure Automation a stahování aktualizací vyžaduje otevřený port 443.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Účet Automation](./automation-offering-get-started.md)
* Povolený [hybridní pracovní proces runbooku](./automation-hybrid-runbook-worker.md) na virtuálním počítači

Otevře se obrazovka řešení **Update Management**. Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

![Okno Povolit řešení Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

Povolení řešení může trvat až několik minut. Během této doby byste neměli zavírat okno prohlížeče.
Po povolení řešení začnou do Log Analytics proudit informace o chybějících aktualizacích na virtuálních počítačích.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení **správy aktualizací** se zobrazí obrazovka **Správa aktualizací**.
Pokud nějaké aktualizace chybí, na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

Vyberte **ODKAZ NA INFORMACE** na nějaké aktualizaci a v novém okně se otevře článek podpory o dané aktualizaci. Tady najdete důležité informace týkající se dané aktualizace.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Po kliknutí kamkoli jinam na aktualizaci se otevře okno **Prohledávání protokolu** pro vybranou aktualizaci. Dotaz pro prohledávání protokolu je předdefinovaný pro tuto konkrétní aktualizaci. Tento dotaz můžete upravit nebo vytvořit vlastní dotaz a zobrazit podrobné informace o nasazených nebo chybějících aktualizacích ve vašem prostředí.

![Zobrazení stavu aktualizace](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Konfigurace upozorňování

V tomto kroku nakonfigurujete upozornění, které vás bude informovat o úspěšném nasazení aktualizací. Upozornění, které vytvoříte, je založené na dotazu Log Analytics. Můžete napsat jakýkoli vlastní dotaz pro další upozornění, které budou pokrývat řadu různých scénářů. Na webu Azure Portal přejděte do části **Monitorování** a klikněte na **Vytvořit upozornění**. Tím se otevře stránka **Vytvořit pravidlo**.

V části **1. Definujte podmínku upozornění** klikněte na **+ Vybrat cíl**. V části **Filtrovat podle typu prostředku** vyberte **Log Analytics**. Zvolte váš pracovní prostor Log Analytics a klikněte na **Hotovo**.

![vytvoření upozornění](./media/automation-tutorial-update-management/create-alert.png)

Kliknutím na tlačítko **+ Přidat kritéria** otevřete stránku **Konfigurovat logiku signálů**. V tabulce zvolte **Vlastní prohledávání protokolu**. Do textového pole **Vyhledávací dotaz** zadejte následující dotaz. Tento dotaz vrátí počítače a název hromadné postupné aktualizace dokončené v zadaném časovém rámci.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Jako **Prahovou hodnotu** pro logiku upozornění zadejte **1**. Jakmile budete hotovi, klikněte na **Hotovo**.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/signal-logic.png)

V části **2. Definujte podrobnosti upozornění** zadejte popisný název a popis upozornění. Vzhledem k tomu, že je upozornění určené pro úspěšné spuštění, nastavte **Závažnost** na **Informativní (záv. 2)**.

![Konfigurace logiky signálů](./media/automation-tutorial-update-management/define-alert-details.png)

V části **3. Definujte skupinu akcí** klikněte na **+ Nová skupina akcí**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Můžou mezi ně patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).

Do pole **Název skupiny akcí** zadejte popisný a krátký název. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

V části **Akce** zadejte popisný název akce, například **E-mailová oznámení**, a v části **TYP AKCE** vyberte **E-mailové/SMS/nabízené/hlasové oznámení**. V části **PODROBNOSTI** vyberte **Upravit podrobnosti**.

Na stránce **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu, která se má použít.

![Konfigurace e-mailové skupiny akcí](./media/automation-tutorial-update-management/configure-email-action-group.png)

Kliknutím na **OK** zavřete stránku **E-mailové/SMS/nabízené/hlasové oznámení** a kliknutím na **OK** zavřete stránku **Přidat skupinu akcí**.

Předmět odeslaného e-mailu můžete upravit kliknutím na **Předmět e-mailu** v části **Přizpůsobit akce** na stránce **Vytvořit pravidlo**. Jakmile budete hotovi, klikněte na **Vytvořit pravidlo upozornění**. Tím se vytvoří pravidlo, které vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Teď, když je nakonfigurované upozorňování, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro instalaci aktualizací.
Můžete zvolit typy aktualizací, které budou součástí nasazení.
Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

> [!WARNING]
> Pokud aktualizace vyžadují restartování, virtuální počítač se restartuje automaticky.

Naplánujte pro virtuální počítač nové nasazení aktualizací tak, že přejdete do části **Správa aktualizací** a vyberete **Naplánovat nasazení aktualizací v horní části obrazovky**.

Na obrazovce **Nové nasazení aktualizací** zadejte následující informace :

* **Název** – Zadejte jedinečný název pro identifikaci nasazení aktualizace.

* **Operační systém** –Zvolte cílový operační systém pro nasazení aktualizací.

* **Klasifikace aktualizací** – Vyberte typy softwaru, které se zahrnou do nasazení aktualizací. Pro účely tohoto kurzu nechte vybrané všechny typy.

  Typy klasifikace jsou:

   |Operační systém  |Typ  |
   |---------|---------|
   |Windows     | Důležité aktualizace</br>Aktualizace zabezpečení</br>Kumulativní aktualizace</br>Balíčky funkcí</br>Aktualizace Service Pack</br>Aktualizace definic</br>Nástroje</br>Aktualizace        |
   |Linux     | Důležité aktualizace a aktualizace zabezpečení</br>Další aktualizace       |

   Popis typů klasifikace najdete v tématu popisujícím [klasifikace aktualizací](automation-update-management.md#update-classifications).

* **Nastavení plánu** – Tím se otevře stránka Nastavení plánu. Výchozí čas spuštění je 30 minut po aktuálním čase. Můžete ho nastavit na jakýkoli čas minimálně 10 minut po aktuálním čase.

   Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování.
   V části **Opakování** vyberte **Jednou**. Ponechte výchozí hodnotu 1 den a klikněte na **OK**. Tím se nastaví opakovaný plán.

* **Časové období údržby (minuty)** – Tady ponechte výchozí hodnotu. Můžete zadat časové období, ve kterém má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

![Obrazovka nastavení plánu aktualizací](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Jakmile dokončíte konfiguraci plánu, klikněte na tlačítko **Vytvořit**. Vrátíte se na řídicí panel stavu. Vyberte **Naplánovaná nasazení aktualizací** a zobrazte plán nasazení, který jste vytvořili.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** na obrazovce **Správa aktualizací**.
Pokud je nasazení aktuálně spuštěno, jeho stav je **Probíhající**.
Po úspěšném dokončení se změní na **Úspěch**.
Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.
Kliknutím na dokončené nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazování aktualizací pro konkrétní nasazení](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Na dlaždici **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači.
V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace.
Následující seznam ukazuje dostupné hodnoty:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože podle definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch** – Aktualizace byla úspěšná.
* **Neúspěch** – Aktualizace se nezdařila.

Kliknutím na **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Kliknutím na dlaždici **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách nasazení.

Po úspěšném nasazení aktualizací se odešle podobný e-mail jako na následujícím obrázku, který značí úspěch nasazení.

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
