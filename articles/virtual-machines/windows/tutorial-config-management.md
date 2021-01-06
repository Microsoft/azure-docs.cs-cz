---
title: Kurz – Správa konfigurace virtuálních počítačů s Windows v Azure
description: V tomto kurzu zjistíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači s Windows.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f9f37bc27c54971c15db457abf157e04cc5d60e8
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914667"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Kurz: monitorování změn a aktualizace virtuálního počítače s Windows v Azure

Díky Azure [Change Tracking](../../automation/change-tracking/overview.md) a [Update Management](../../automation/update-management/overview.md)můžete snadno identifikovat změny ve virtuálních počítačích s Windows v Azure a spravovat aktualizace operačního systému pro tyto virtuální počítače.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Správa aktualizací Windows.
> * Sledování změn a inventáře.

## <a name="open-azure-cloud-shell"></a>Otevření služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem Azure.

Chcete-li otevřít libovolný blok kódu v Cloud Shell, stačí vybrat příkaz **vyzkoušet** v pravém horním rohu tohoto bloku kódu.

Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložte je na kartu Cloud Shell a stiskněte klávesu ENTER pro spuštění kódu.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Ke konfiguraci monitorování a správy aktualizací Azure v tomto kurzu budete potřebovat virtuální počítač s Windows v Azure.

Nejdřív pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1&preserve-view=true) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Pak vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem `myVM` v `East US` umístění. Pokud ještě neexistují, vytvoří se skupina prostředků `myResourceGroupMonitor` a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="manage-windows-updates"></a>Správa aktualizací pro Windows

Update Management vám pomůže spravovat aktualizace a opravy pro virtuální počítače Azure s Windows. Přímo z virtuálního počítače můžete rychle:

- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolujte výsledky nasazení a ověřte, jestli se aktualizace úspěšně nastavily na virtuálním počítači.

Informace o cenách najdete v tématu [ceny služby Automation pro správu aktualizací](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Povolení řešení Update Management

Povolení Update Management pro virtuální počítač:

1. Na levé straně okna vyberte **virtuální počítače**.
1. V seznamu vyberte virtuální počítač.
1. V podokně **operace** v okně virtuálního počítače vyberte **Správa aktualizací**.
1. Otevře se okno **povolit Update Management** .

K určení, jestli je pro tento virtuální počítač povolená Update Management, se provede ověření. Ověřování zahrnuje kontroly Log Analytics pracovního prostoru pro propojený účet Automation a pro to, jestli je řešení v pracovním prostoru.

Pracovní prostor [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) slouží ke shromažďování dat generovaných funkcemi a službami, jako je například Update Management. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

K provedení dalších akcí na virtuálních počítačích, které vyžadují aktualizace, můžete použít Azure Automation ke spouštění Runbooků na virtuálních počítačích. Mezi tyto akce patří stahování nebo použití aktualizací.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s Microsoft Monitoring Agent (MMA) a Hybrid Runbook Workerem pro automatizaci. Pomocí agenta můžete komunikovat s virtuálním počítačem a získat informace o stavu aktualizace.

V okně **povolit Update Management** zvolte pracovní prostor Log Analytics a účet Automation a potom vyberte **Povolit**. Pro toto řešení trvá až 15 minut, než se aktivuje.

Automaticky se přidávají některé z následujících požadavků, které během připojování chybí:

* Pracovní prostor [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)
* [Automation](../../automation/index.yml)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md), který je ve virtuálním počítači povolený

Po povolení řešení se otevře okno **Správa aktualizací** . Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a pak vyberte **Povolit**. Pokud se tyto možnosti zobrazí šedě, pro virtuální počítač je povolené jiné řešení automatizace a musí se použít pracovní prostor řešení a účet Automation.

![Povolit Update Management řešení](./media/tutorial-monitoring/manageupdates-update-enable.png)

Řešení Update Management může trvat až 15 minut, než se aktivuje. Během této doby nezavírejte okno prohlížeče. Po povolení řešení budou informace o chybějících aktualizacích na virtuálním počítači Azure Monitor protokoly. Může trvat 30 minut až 6 hodin, než se data budou k dispozici pro analýzu.

### <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení Update Management se zobrazí okno **Správa aktualizací** . Po dokončení vyhodnocení aktualizací se na kartě **chybějící aktualizace** zobrazí seznam chybějících aktualizací.

 ![Zobrazení stavu aktualizace](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu. Zvolíte, které typy aktualizací se mají zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Pokud chcete naplánovat nové nasazení aktualizace pro virtuální počítač, vyberte **naplánovat nasazení aktualizací** v horní části okna **Update Management** . V okně **nové nasazení aktualizací** zadejte následující informace:

| Možnost | Popis |
| --- | --- |
| **Název** |Zadejte jedinečný název pro identifikaci nasazení aktualizace. |
|**Operační systém**| Vyberte možnost **Linux** nebo **Windows**.|
| **Skupiny, které se mají aktualizovat** |U virtuálních počítačů hostovaných v Azure definujte dotaz na základě kombinace předplatného, skupin prostředků, umístění a značek. Tento dotaz vytvoří dynamickou skupinu virtuálních počítačů hostovaných v Azure, které se mají zahrnout do nasazení. </br></br>U virtuálních počítačů, které nejsou hostované v Azure, vyberte existující uložené výsledky hledání. Pomocí tohoto hledání můžete vybrat skupinu těchto virtuálních počítačů, které chcete zahrnout do nasazení. </br></br> Další informace najdete v tématu [dynamické skupiny](../../automation/update-management/configure-groups.md).|
| **Počítače, které se mají aktualizovat** |Vyberte **uložené hledání**, **importované skupiny** nebo **počítače**.<br/><br/>Pokud vyberete možnost **počítače**, můžete z rozevíracího seznamu zvolit jednotlivé počítače. Připravenost jednotlivých počítačů se zobrazí ve sloupci **připravenosti agenta aktualizace** v tabulce.</br></br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|**Update classifications**|Vyberte všechny potřebné klasifikace aktualizací.|
|**Zahrnout nebo vyloučit aktualizace**|Tuto možnost vyberte, pokud chcete otevřít podokno **zahrnutí/vyloučení** . Aktualizace, které se mají zahrnout, a ty, které se mají vyloučit, jsou na různých kartách. Další informace o způsobu zpracování zahrnutí najdete v tématu [Naplánování nasazení aktualizací](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Nastavení plánu**|Zvolte čas **spuštění a vyberte jeden nebo znovu** **.**|
| **Pre-Scripts + post-Scripts**|Vyberte skripty, které se spustí před nasazením a po něm.|
| **Časové období údržby** | Zadejte počet minut, po které se nastavují aktualizace. Platné hodnoty jsou v rozsahu od 30 do 360 minut. |
| **Restartovat ovládací prvek**| Vyberte způsob zpracování restartování. Dostupné výběry:<ul><li>**Restartovat v případě potřeby**</li><li>**Vždy restartovat**</li><li>**Nikdy nerestartovat**</li><li>**Jenom restartování**</li></ul>V **případě potřeby je nutné restartovat** výchozí výběr. Vyberete-li možnost **pouze restartovat**, aktualizace nejsou nainstalovány.|

Po dokončení konfigurace plánu klikněte na **vytvořit** a vraťte se na řídicí panel stavu. **Naplánovaná** tabulka zobrazuje plán nasazení, který jste vytvořili.

Nasazení aktualizací můžete také vytvořit programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou můžete použít k vytvoření týdenního nasazení aktualizací. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav nasazení zobrazí na kartě **nasazení aktualizací** okna **Správa aktualizací** .

Pokud je nasazení aktuálně spuštěno, jeho stav se zobrazuje "probíhá". Po úspěšném dokončení se stav změní na úspěch. Pokud ale některé aktualizace v nasazení selžou, stav je "částečně neúspěšné".

Výběrem dokončeného nasazení aktualizace zobrazíte řídicí panel pro toto nasazení.

![Řídicí panel stavu nasazování aktualizací pro konkrétní nasazení](./media/tutorial-monitoring/manageupdates-view-results.png)

Na dlaždici **výsledky aktualizace** se zobrazí souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači. V tabulce vpravo se zobrazuje podrobný rozpis každé aktualizace a výsledky instalace. Každý výsledek má jednu z následujících hodnot:

* **Nepokus**: aktualizace není nainstalovaná. Na základě definované doby trvání okna údržby nebyla k dispozici dostatek času.
* **Úspěch**: aktualizace byla úspěšná.
* **Chyba**: aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem dlaždice **výstup** zobrazíte datový proud úlohy Runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Pokud chcete zobrazit podrobné informace o chybách nasazení, vyberte **chyby** .

## <a name="monitor-changes-and-inventory"></a>Monitorování změn a inventáře

Na svých počítačích můžete shromažďovat a zobrazovat inventář softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows. Sledování konfigurací vašich počítačů vám pomůže odhalit provozní problémy v celém prostředí a lépe pochopit stav počítačů.

### <a name="enable-change-and-inventory-management"></a>Povolení správy změn a inventáře

Povolení správy změn a inventáře pro virtuální počítač:

1. Na levé straně okna vyberte **virtuální počítače**.
1. V seznamu vyberte virtuální počítač.
1. V části **operace** v okně virtuálního počítače vyberte **inventarizace** nebo **sledování změn**.
1. Otevře se podokno **povolit Change Tracking a inventář** .

Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a pak vyberte **Povolit**. Pokud jsou možnosti nedostupné, je pro tento virtuální počítač už povolené řešení pro automatizaci. V takovém případě musí být použit již povolený pracovní prostor a účet Automation.

I když se řešení v nabídce objeví samostatně, jedná se o stejné řešení. Povolením jednoho se na virtuálním počítači povolí obě.

![Povolení sledování změn a inventáře](./media/tutorial-monitoring/manage-inventory-enable.png)

Jakmile je řešení povolené, může trvat nějakou dobu, než se na virtuálním počítači shromáždí inventář předtím, než se zobrazí data.

### <a name="track-changes"></a>Sledování změn

Na svém VIRTUÁLNÍm počítači v části **operace** vyberte **Change Tracking** a pak vyberte **Upravit nastavení**. Otevře se podokno **Change Tracking** . Vyberte typ nastavení, které chcete sledovat, a vybráním možnosti **+ Přidat** nastavení nakonfigurujte.

K dispozici jsou možnosti nastavení pro Windows:

* Registr Windows
* Soubory Windows

Podrobné informace o Change Tracking najdete v tématu [řešení potíží se změnami na virtuálním počítači](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Zobrazení inventáře

Na svém VIRTUÁLNÍm počítači vyberte **inventarizace** v části **operace**. Na kartě **software** je k dispozici tabulka s informacemi o zjištěném softwaru. V tabulce se zobrazí podrobnosti vysoké úrovně pro každý záznam softwaru. Mezi tyto podrobnosti patří název softwaru, verze, Vydavatel a čas poslední aktualizace.

![Zobrazení inventáře](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorování protokolů aktivit a změn

V okně **Change Tracking (sledování změn** ) na vašem virtuálním počítači vyberte **Spravovat připojení protokolu aktivit** a otevřete podokno **protokolu aktivit Azure** . Vyberte **připojit** a připojte Change Tracking k protokolu aktivit Azure pro váš virtuální počítač.

Po povolení Change Tracking pro váš virtuální počítač přejít do podokna **Přehled** a výběrem možnosti **zastavit** zastavte virtuální počítač. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Až se virtuální počítač oddělí, vyberte **Spustit** a restartujte virtuální počítač.

Zastavení a restartování virtuálního počítače zapíše událost do protokolu aktivit. Vraťte se do podokna **sledování změn** a v dolní části podokna vyberte kartu **události** . Po chvíli se události zobrazí v grafu a tabulce. Můžete vybrat jednotlivé události a zobrazit tak podrobné informace o této události.

![Zobrazení změn v protokolu aktivit](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Předchozí graf zobrazuje změny, ke kterým došlo v průběhu času. Po přidání připojení protokolu aktivit Azure zobrazí spojnicový graf v horní části události protokolu aktivit Azure.

Každý řádek pruhových grafů představuje jiný typ změny, který se může sledovat. Mezi tyto typy patří procesy démonů systému Linux, soubory, klíče registru Windows, software a služby systému Windows. Na kartě **Změna** se zobrazí podrobnosti o změně. Změny se zobrazí v pořadí, kdy došlo k jednotlivým navýšení, s poslední změnou zobrazenou jako první.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zkontrolovali Change Tracking a Update Management pro svůj virtuální počítač. Naučili jste se:

> [!div class="checklist"]
> * Vytvořte skupinu prostředků a virtuální počítač.
> * Správa aktualizací Windows.
> * Sledování změn a inventáře.

Další informace o monitorování virtuálního počítače najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitor.md)
