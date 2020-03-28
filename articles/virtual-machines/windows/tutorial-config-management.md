---
title: Kurz – Správa konfigurace virtuálního počítače s Windows v Azure
description: V tomto kurzu se dozvíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači se systémem Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238577"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Kurz: Sledování změn a aktualizace virtuálního počítače s Windows v Azure

Pomocí [Azure Change Tracking](../../automation/change-tracking.md) and [Update Management](../../automation/automation-update-management.md)můžete snadno identifikovat změny ve virtuálních počítačích s Windows v Azure a spravovat aktualizace operačního systému pro tyto virtuální počítače.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Správa aktualizací systému Windows.
> * Sledujte změny a inventář.

## <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má běžné nástroje Azure předinstalované a nakonfigurované pro použití s vaším účtem Azure.

Chcete-li otevřít libovolný blok kódu v prostředí Cloud Shell, stačí vybrat **Try it** v pravém horním rohu tohoto bloku kódu.

Cloud Shell můžete otevřít také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Vyberte **Kopírovat,** chcete-li zkopírovat bloky kódu, vložte je na kartu Cloud Shell a vyberte klávesu Enter pro spuštění kódu.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Ke konfiguraci monitorování a správy aktualizací Azure v tomto kurzu budete potřebovat virtuální počítač s Windows v Azure.

Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Dále vytvořte virtuální virtuální město s [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální `myVM` hod `East US` pojmenovaný v umístění. Pokud ještě neexistují, vytvoří se `myResourceGroupMonitor` skupina prostředků a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="manage-windows-updates"></a>Správa aktualizací pro Windows

Správa aktualizací vám pomůže spravovat aktualizace a opravy pro virtuální počítače Azure windows. Přímo z virtuálního počítače můžete rychle:

- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolujte výsledky nasazení a ověřte, zda byly aktualizace úspěšně použity pro virtuální počítače.

Informace o cenách naleznete v [tématu Automation pricing for Update management](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Povolení řešení Update Management

Povolení správy aktualizací pro váš virtuální počítač:

1. Na levé straně okna vyberte **Virtuální počítače**.
1. Vyberte virtuální ho v seznamu.
1. V podokně **Operace** v okně Virtuální ho virtuálního aplikace vyberte **možnost Správa aktualizací**.
1. Otevře se okno **Povolit správu aktualizací.**

Ověření se provádí k určení, pokud je pro tento virtuální virtuální byl povolen správu aktualizací. Ověření zahrnuje kontroly pracovního prostoru Analýzy protokolů, propojeného účtu automatizace a toho, zda je řešení v pracovním prostoru.

Pracovní prostor [Analýzy protokolů](../../log-analytics/log-analytics-overview.md) slouží ke shromažďování dat generovaných funkcemi a službami, jako je správa aktualizací. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Chcete-li provádět další akce na virtuálních počítačích, které vyžadují aktualizace, můžete použít Azure Automation ke spuštění runbooků proti virtuálním počítačům. Tyto akce zahrnují stahování nebo použití aktualizací.

Proces ověření také zkontroluje, jestli je virtuální ms zřízen s Microsoft Monitoring Agent (MMA) a automatizace hybridní runbook worker. Agent slouží ke komunikaci s virtuálním virtuálním min. a získání informací o stavu aktualizace.

V okně **Povolit správu aktualizací** zvolte pracovní prostor Analýzy protokolů a účet automatizace a pak vyberte **Povolit**. Řešení trvá až 15 minut, než se aktivuje.

Všechny následující požadavky, které při zapisování chybí, jsou automaticky přidány:

* Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatizace](../../automation/automation-offering-get-started.md)
* Hybridní [pracovník runbooku](../../automation/automation-hybrid-runbook-worker.md), který je povolený na virtuálním počítači

Po povolení řešení se otevře okno **správy aktualizací.** Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automatizace, který chcete použít, a pak vyberte **Povolit**. Pokud se tyto možnosti zobrazí šedě, je pro virtuální modul povoleno jiné řešení automatizace a musí se použít účet pracovního prostoru a automatizace tohoto řešení.

![Povolit řešení správy aktualizací](./media/tutorial-monitoring/manageupdates-update-enable.png)

Povolení řešení správy aktualizací může trvat až 15 minut. Během této doby nezavírejte okno prohlížeče. Po povolení řešení informace o chybějící aktualizace na virtuálním počítači toky do protokolů Azure Monitor. Může trvat 30 minut až 6 hodin, než budou data k dispozici pro analýzu.

### <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení správy aktualizací se zobrazí okno **Správa aktualizací.** Po dokončení vyhodnocení aktualizací se na kartě **Chybějící aktualizace** zobrazí seznam chybějících aktualizací.

 ![Zobrazení stavu aktualizace](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu. Můžete zvolit, které typy aktualizací chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Chcete-li naplánovat nasazení nové aktualizace pro virtuální počítače, vyberte **naplánovat nasazení aktualizace** v horní části okna **správy aktualizací.** V okně **Nasazení nové aktualizace** zadejte následující informace:

| Možnost | Popis |
| --- | --- |
| **Název** |Zadejte jedinečný název pro identifikaci nasazení aktualizace. |
|**Operační systém**| Vyberte **linuxovou** nebo **windowsovou**konzoli .|
| **Skupiny k aktualizaci** |Pro virtuální počítače hostované v Azure definujte dotaz na základě kombinace předplatného, skupin prostředků, umístění a značek. Tento dotaz vytvoří dynamickou skupinu virtuálních počítačích hostovaných v Azure, které budou zahrnuty do vašeho nasazení. </br></br>Pro virtuální počítače, které nejsou hostované v Azure, vyberte existující uložené hledání. Pomocí tohoto hledání můžete vybrat skupinu těchto virtuálních počítače, které chcete zahrnout do nasazení. </br></br> Další informace naleznete v [tématu Dynamické skupiny](../../automation/automation-update-management-groups.md).|
| **Počítače k aktualizaci** |Vyberte **Uložené hledání**, **Importovaná skupina**nebo **Počítače**.<br/><br/>Pokud vyberete **stroje**, můžete si vybrat jednotlivé stroje z rozevíracího seznamu. Připravenost každého stroje je uvedena ve sloupci **PŘIPRAVENOST UPDATE AGENT** v tabulce.</br></br> Informace o různých způsobech vytváření skupin počítačů v protokolech Azure Monitoru najdete [v tématu Skupiny počítačů v protokolech Azure Monitoru.](../../azure-monitor/platform/computer-groups.md) |
|**Aktualizovat klasifikace**|Zvolte všechny potřebné klasifikace aktualizací.|
|**Zahrnout/vyloučit aktualizace**|Tuto možnost vyberte, chcete-li otevřít podokno **Zahrnout nebo vyloučit.** Aktualizace, které mají být zahrnuty, a ty, které mají být vyloučeny, jsou na samostatných kartách. Další informace o tom, jak se provádí zahrnutí, naleznete v [tématu Plánování nasazení aktualizace](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Nastavení plánu**|Vyberte čas, kdy chcete začít, a vyberte **možnost Jednou** nebo **Opakovat**.|
| **Pre-skripty + Post-skripty**|Vyberte skripty, které chcete spustit před a po nasazení.|
| **Okno údržby** | Zadejte počet minut nastavený pro aktualizace. Platné hodnoty jsou v rozsahu od 30 do 360 minut. |
| **Řízení restartování**| Vyberte způsob zpracování restartování. Dostupné výběry jsou:<ul><li>**V případě potřeby restartujte počítač**</li><li>**Vždy restartovat**</li><li>**Nikdy nerestartovat**</li><li>**Pouze restartovat počítač**</li></ul>**V případě potřeby** je výchozí výběr restartován. Pokud vyberete **Pouze restartovat**počítač , aktualizace se nenainstalují.|

Po dokončení konfigurace plánu se kliknutím na **Vytvořit** vraťte na stavový řídicí panel. **Naplánovaná** tabulka zobrazuje plán nasazení, který jste vytvořili.

Můžete také vytvořit nasazení aktualizací programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí rozhraní REST API, naleznete v [tématu Konfigurace aktualizací softwaru – vytvoření](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také ukázkový runbook, který můžete použít k vytvoření týdenní aktualizace nasazení. Další informace o tomto runbooku najdete [v tématu Vytvoření týdenní aktualizace nasazení pro jeden nebo více virtuálních počítače ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po zahájení plánovaného nasazení se stav nasazení zobrazí na kartě **Aktualizace nasazení** v okně **Správa aktualizací.**

Pokud je nasazení aktuálně spuštěno, jeho stav se zobrazí jako Probíhá. Po úspěšném dokončení se stav změní na "Úspěšné". Ale pokud všechny aktualizace v nasazení nezdaří, stav je "Částečně se nezdařilo."

Vyberte dokončené nasazení aktualizace, chcete-li zobrazit řídicí panel pro toto nasazení.

![Řídicí panel stavu nasazování aktualizací pro konkrétní nasazení](./media/tutorial-monitoring/manageupdates-view-results.png)

Dlaždice **Výsledky aktualizace** zobrazuje souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo je uveden podrobný rozpis jednotlivých aktualizací a výsledků instalace. Každý výsledek má jednu z následujících hodnot:

* **Nepokusil:** Aktualizace není nainstalována. Na základě definované doby trvání okna údržby nebyl k dispozici dostatek času.
* **Úspěšné**: Aktualizace byla úspěšná.
* **Nezdařilo**se: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Vyberte dlaždici **Výstup,** chcete-li zobrazit datový proud úloh sady Runbook zodpovědný za správu nasazení aktualizace na cílovém virtuálním počítači.

Výběrem **možnosti Chyby** zobrazíte podrobné informace o chybách nasazení.

## <a name="monitor-changes-and-inventory"></a>Monitorování změn a inventáře

Můžete shromažďovat a zobrazovat inventář softwaru, souborů, daemonů Linuxu, služeb systému Windows a klíčů registru systému Windows v počítačích. Sledování konfigurací vašich počítačů vám pomůže určit provozní problémy ve vašem prostředí a lépe pochopit stav vašich počítačů.

### <a name="enable-change-and-inventory-management"></a>Povolit správu změn a zásob

Povolení změny a řízení zásob pro váš virtuální počítač:

1. Na levé straně okna vyberte **Virtuální počítače**.
1. Vyberte virtuální ho v seznamu.
1. V části **Operace** v okně Virtuální ho virtuálního motoru vyberte **sledování zásob** nebo **změn**.
1. Otevře se podokno **Povolit sledování změn a inventář.**

Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automatizace, který chcete použít, a pak vyberte **Povolit**. Pokud se možnosti zobrazují šedě, je řešení automatizace již povoleno pro virtuální modul. V takovém případě musí být použit již povolený účet pracovního prostoru a automatizace.

I když se řešení v nabídce zobrazují samostatně, jsou to stejné řešení. Povolením jednoho se na virtuálním počítači povolí obě.

![Povolení sledování změn a inventáře](./media/tutorial-monitoring/manage-inventory-enable.png)

Po povolení řešení může nějakou dobu trvat, než se na virtuálním počítači shromaždí inventář, než se zobrazí data.

### <a name="track-changes"></a>Sledování změn

Na virtuálním počítači v části **OPERACE**vyberte **Změnit sledování** a pak **vyberte Upravit nastavení**. Otevře se podokno **Sledování změn.** Vyberte typ nastavení, které chcete sledovat, a vybráním možnosti **+ Přidat** nastavení nakonfigurujte.

Dostupné možnosti nastavení systému Windows jsou:

* Registr Windows
* Soubory Windows

Podrobné informace o sledování změn najdete [v tématu Poradce při potížích se změnami na virtuálním počítači](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Zobrazení inventáře

Na vašem virtuálním počítači vyberte **Zásoby** v části **OPERACE**. Na kartě **Software** je tabulka s nalezeným softwarem. Podrobnosti vysoké úrovně pro každý softwarový záznam se zobrazí v tabulce. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel a naposledy aktualizovaný čas.

![Zobrazení inventáře](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Sledování protokolů a změn aktivit

V okně **Sledování změn** na virtuálním počítači vyberte spravovat připojení **protokolu aktivit,** abyste otevřeli podokno **protokolu aktivit Azure.** Vyberte **Připojit,** chcete-li připojit sledování změn k protokolu aktivit Azure pro váš virtuální počítač.

Po povolení sledování změn přejděte do podokna **Přehled** pro virtuální počítač a vyberte **Zastavit,** chcete-li virtuální počítač zastavit. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Po nabytá virtuálním počítači vyberte **Spustit** restartování virtuálního počítače.

Zastavení a restartování virtuálního aplikace zaznamená událost do protokolu aktivit. Vraťte se do podokna **Sledování změn** a vyberte kartu **Události** v dolní části podokna. Po chvíli se události zobrazí v grafu a tabulce. Můžete vybrat každou událost a zobrazit podrobné informace pro tuto událost.

![Zobrazení změn v protokolu aktivit](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Předchozí graf zobrazuje změny, ke kterým došlo v průběhu času. Po přidání připojení protokolu aktivit Azure řádkový graf v horní části zobrazí události protokolu aktivit Azure.

Každý řádek pruhových grafů představuje jiný sledovaný typ změny. Jedná se o linuxové daemony, soubory, klíče registru systému Windows, software a služby systému Windows. Na kartě **Změna** se zobrazí podrobnosti o změně. Změny se zobrazí v pořadí, kdy k jednotlivým došlo, přičemž poslední změna byla zobrazena jako první.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zkontrolovali sledování změn a správu aktualizací pro váš virtuální počítač. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvořte skupinu prostředků a virtuální hod.
> * Správa aktualizací systému Windows.
> * Sledujte změny a inventář.

Přejděte na další kurz, kde se dozvíte o monitorování virtuálního počítače.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitor.md)
