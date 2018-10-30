---
title: Kurz – Monitorování a aktualizace virtuálních počítačů s Windows v Azure | Microsoft Docs
description: V tomto kurzu se naučíte, jak monitorovat diagnostiku spouštění a metriky výkonu a jak spravovat aktualizace balíčků na virtuálním počítači s Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ce90ea447f7dcf4df1451294acf9f7fd093ad6ee
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408638"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>Kurz: Monitorování a aktualizace virtuálních počítačů s Windows v Azure

Monitorování Azure prostřednictvím agentů shromažďuje data spouštění a výkonu z virtuálních počítačů Azure, ukládá tato data do úložiště Azure a zpřístupňuje je přes portál, modul Azure PowerShell a Azure CLI. Správa aktualizací umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Windows.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele virtuálního počítače
> * Instalace diagnostického rozšíření
> * Zobrazení metrik virtuálního počítače
> * Vytvoření upozornění
> * Správa aktualizací pro Windows
> * Monitorování změn a inventáře
> * Nastavení pokročilého monitorování

Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Ke konfiguraci monitorování a správy aktualizací Azure v tomto kurzu budete potřebovat virtuální počítač s Windows v Azure. Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní vytvořte virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupMonitorMonitor* a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="view-boot-diagnostics"></a>Zobrazení diagnostiky spouštění

Při spouštění virtuálních počítačů s Windows zachytí agent diagnostiky spouštění výstup na obrazovce, který můžete použít pro účely řešení potíží. Tato funkce je ve výchozím nastavení zapnuta. Zachycené snímky obrazovky se ukládají do účtu úložiště Azure, které se vytvoří také ve výchozím nastavení.

Diagnostická data spouštění můžete získat pomocí příkazu [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). V následujícím příkladu se diagnostika spouštění stáhne do kořenové složky na jednotce *c:\*.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Windows má vyhrazený virtuální počítač hostitele v Azure, který s ním komunikuje. Metriky se pro hostitele shromažďují automaticky a lze je zobrazit na portálu Azure Portal.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Pokud chcete zjistit, jaký je výkon virtuálního počítače hostitele, klikněte na **Metriky** v okně virtuálního počítače a potom vyberte některou z metrik hostitele v části **Dostupné metriky**.

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalace diagnostického rozšíření

K dispozici máte základní metriky hostitele, ale pokud chcete zobrazit podrobné metriky specifické pro virtuální počítač, musíte na virtuální počítač nainstalovat diagnostické rozšíření Azure. Diagnostické rozšíření Azure umožňuje načítání dalších monitorovacích a diagnostických dat z virtuálního počítače. Tyto metriky výkonu můžete zobrazit a vytvářet výstrahy na základě výkonnosti virtuálního počítače. Diagnostické rozšíření se instaluje prostřednictvím portálu Azure Portal následujícím způsobem:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Klikněte na **Nastavení diagnostiky**. V seznamu je vidět, že *Diagnostika spouštění* je již povolená z předchozí části. Zaškrtněte políčko *Základní metriky*.
3. Klikněte na tlačítko **Povolit monitorování na úrovni hosta**.

    ![Zobrazení diagnostických metrik](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Zobrazení metrik virtuálního počítače

Metriky virtuálního počítače lze zobrazit stejným způsobem jako metriky virtuálního počítače hostitele:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Pokud chcete zjistit, jaký je výkon virtuálního počítače, klikněte na tlačítko **Metriky** v okně virtuálního počítače a pak vyberte některou z diagnostických metrik v části **Dostupné metriky**.

    ![Zobrazení metrik virtuálního počítače](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Klikněte na tlačítko **Pravidla výstrah** v okně virtuálního počítače a potom na **Přidat upozornění metriky** v horní části okna výstrahy.
3. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.
4. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.
5. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.
6. Klikněte na tlačítko **OK**.

## <a name="manage-windows-updates"></a>Správa aktualizací pro Windows

Správa aktualizací umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Windows.
Přímo z virtuálního počítače můžete rychle vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích.

Informace o cenách najdete na stránce s [cenami služby Automation za správu aktualizací](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Povolení řešení Update Management

Povolení řešení Update Management pro virtuální počítač:

1. Na levé straně obrazovky vyberte **Virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **Operace** klikněte na **Update Management**. Otevře se obrazovka **Povolit řešení Update Management**.

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management.
Toto ověření zahrnuje kontroly pracovního prostoru Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení nachází v tomto pracovním prostoru.

Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.
Pokud na virtuálních počítačích, které vyžadují aktualizace, chcete provádět další akce, Azure Automation umožňuje spouštět proti virtuálním počítačům runbooky například pro stahování a aplikování aktualizací.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Microsoft Monitoring Agent (MMA) a hybridním pracovním procesem runbooku Automation.
Agent slouží ke komunikaci s virtuálním počítačem a získávání informací o nainstalovaném softwaru.

Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* Povolený [hybridní pracovní proces runbooku](../../automation/automation-hybrid-runbook-worker.md) na virtuálním počítači

Otevře se obrazovka řešení **Update Management**. Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

![Povolení řešení Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče. Po povolení řešení začnou do Log Analytics proudit informace o chybějících aktualizacích na virtuálních počítačích. Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

### <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení **správy aktualizací** se zobrazí obrazovka **Správa aktualizací**. Po vyhodnocení aktualizací se na kartě **Chybějící aktualizace** zobrazí seznam chybějících aktualizací.

 ![Zobrazení stavu aktualizace](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Naplánujte nové nasazení aktualizací pro virtuální počítač kliknutím na **Naplánovat nasazení aktualizace** v horní části obrazovky **Update Management**. Na obrazovce **Nové nasazení aktualizací** zadejte následující informace :

* **Název** – Zadejte jedinečný název pro identifikaci nasazení aktualizací.
* **Klasifikace aktualizací** – Vyberte typy softwaru, které se zahrnou do nasazení aktualizací. Typy klasifikace jsou:
  * Důležité aktualizace
  * Aktualizace zabezpečení
  * Kumulativní aktualizace
  * Balíčky funkcí
  * Aktualizace Service Pack
  * Aktualizace definic
  * Nástroje
  * Aktualizace

* **Nastavení plánu** – Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času, nebo zadat jiný čas.
  Můžete také určit, jestli nasazení proběhne jednou, nebo nastavit plán opakování. Pokud chcete nastavit plán opakování, klikněte na možnost Opakovat v části Opakování.

  ![Obrazovka nastavení plánu aktualizací](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Časové období údržby (minuty)** – Zadejte časové období, ve kterém má dojít k nasazení aktualizací.  Pomůžete tím zajistit, že se změny provedou v rámci vašich definovaných časových intervalů pro správu a údržbu.

Jakmile dokončíte konfiguraci plánu, klikněte na tlačítko **Vytvořit** a vrátíte se na řídicí panel stavu.
Všimněte si, že v tabulce **Naplánované** se zobrazí plán nasazení, který jste vytvořili.

> [!WARNING]
> V případě aktualizací, které vyžadují restartování, se virtuální počítač restartuje automaticky.

### <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** na obrazovce **Správa aktualizací**.
Pokud je nasazení aktuálně spuštěno, jeho stav je **Probíhající**. Po úspěšném dokončení se změní na **Úspěch**.
Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.
Kliknutím na dokončené nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazování aktualizací pro konkrétní nasazení](./media/tutorial-monitoring/manageupdates-view-results.png)

Na dlaždici **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači.
V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace, které můžou mít jednu z následujících hodnot:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože podle definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch** – Aktualizace byla úspěšná.
* **Neúspěch** – Aktualizace se nezdařila.

Kliknutím na **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Kliknutím na dlaždici **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách nasazení.

## <a name="monitor-changes-and-inventory"></a>Monitorování změn a inventáře

Můžete shromažďovat a zobrazovat inventář softwaru, souborů, linuxových procesů démon, služeb systému Windows a klíčů registru Windows na vašich počítačích. Sledování konfigurací vašich počítačů vám může pomoci přesně identifikovat provozní problémy napříč prostředím a lépe porozumět stavu vašich počítačů.

### <a name="enable-change-and-inventory-management"></a>Povolení správy změn a inventáře

Povolení správy změn a inventáře pro virtuální počítač:

1. Na levé straně obrazovky vyberte **Virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **Operace** klikněte na **Inventory** nebo **Change Tracking**. Otevře se obrazovka **Povolit řešení Change Tracking a Inventory**.

Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation. I když jsou řešení v nabídce oddělená, stále se jedná o stejné řešení. Povolením jednoho se na virtuálním počítači povolí obě.

![Povolení sledování změn a inventáře](./media/tutorial-monitoring/manage-inventory-enable.png)

Po povolení řešení může shromažďování soupisu na virtuálním počítači nějakou dobu trvat, a až pak se zobrazí data.

### <a name="track-changes"></a>Sledování změn

Na svém virtuálním počítači v části **OPERACE** vyberte **Sledování změn**. Klikněte na **Upravit nastavení** a zobrazí se stránka **Change Tracking**. Vyberte typ nastavení, které chcete sledovat, a kliknutím na **+ Přidat** nakonfigurujte nastavení. Dostupné možnosti pro Windows:

* Registr Windows
* Soubory Windows

Podrobné informace o řešení Change Tracking najdete v tématu [Řešení potíží se změnami na virtuálním počítači](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Zobrazení inventáře

Na svém virtuálním počítači v části **OPERACE** vyberte **Soupis**. Na kartě **Software** je tabulkový seznam nalezeného softwaru. V tabulce jsou zobrazené základní podrobnosti o jednotlivých záznamech softwaru. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel a čas poslední aktualizace.

![Zobrazení inventáře](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorování protokolů aktivit a změn

Na stránce **Change Tracking** na vašem virtuálním počítači vyberte **Správa připojení protokolu aktivit**. Tato úloha otevře stránku **Protokol aktivit Azure**. Vyberte **Připojit** a propojte řešení Change Tracking s protokolem aktivit Azure pro váš virtuální počítač.

Když je toto nastavení povolené, přejděte na stránku **Přehled** vašeho virtuálního počítače a výběrem **Zastavit** virtuální počítač zastavte. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Až bude přidělení vašeho virtuálního počítače zrušeno, vyberte **Spustit** a restartujte ho.

Zastavení a spuštění virtuálního počítače zapíše tuto událost do jeho protokolu aktivit Vraťte se na stránku **Change Tracking**. Vyberte **Události** v dolní části stránky. Po chvíli se události zobrazí v grafu a tabulce. Každou událost je možné vybrat a zobrazit o ní podrobné informace.

![Zobrazení změn v protokolu aktivit](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Tento graf ukazuje změny, ke kterým došlo v průběhu času. Po přidání připojení protokolu aktivit zobrazuje čára grafu úplně nahoře události protokolu aktivit Azure. Jednotlivé řádky grafu reprezentují různé typy sledovatelných změn. Tyto typy jsou linuxové procesy démon, soubory, klíče registru systému Windows, software a služby pro Windows. Karta Změny zobrazuje podrobnosti o změnách znázorněných ve vizualizaci v sestupném pořadí podle času, kdy ke změně došlo (nejnovější je první).

## <a name="advanced-monitoring"></a>Pokročilé sledování

K pokročilejšímu monitorování virtuálního počítače můžete použít řešení, jako jsou Update Management, Change Tracking a Inventory, která poskytuje [Azure Automation](../../automation/automation-intro.md).

Pokud máte přístup k pracovnímu prostoru Log Analytics, můžete výběrem možnosti **Upřesnit nastavení** v části **NASTAVENÍ** zjistit klíč a identifikátor pracovního prostoru. Pomocí příkazu [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) přidejte na virtuální počítač rozšíření Microsoft Monitoring Agent. Aktualizujte hodnoty proměnných v níže uvedené ukázce pomocí svého klíče a ID pracovního prostoru Log Analytics.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

Po několika minutách by se nový počítač měl zobrazit v pracovním prostoru Log Analytics.

![Okno Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zkontrolovali virtuální počítače ve službě Azure Security Center. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele
> * Instalace diagnostického rozšíření
> * Zobrazení metrik virtuálního počítače
> * Vytvoření upozornění
> * Správa aktualizací pro Windows
> * Monitorování změn a inventáře
> * Nastavení pokročilého monitorování

V dalším kurzu se dozvíte něco o službě Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálních počítačů](./tutorial-azure-security.md)
