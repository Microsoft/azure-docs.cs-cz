---
title: Azure monitorování a aktualizace a virtuální počítače s Windows | Microsoft Docs
description: Kurz – monitorovat a aktualizovat virtuální počítač Windows v prostředí Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 94151008f0aba6020786e65c60cec66285f310c4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Monitorovat a aktualizovat virtuální počítač s Windows v prostředí Azure PowerShell

Agenty Azure monitorování používá ke shromažďování dat spouštěcí a výkonu z virtuálních počítačů Azure, uložení dat této v úložišti Azure a zpřístupnit prostřednictvím portálu, modulu Azure PowerShell a rozhraní příkazového řádku Azure. Správa aktualizací můžete spravovat aktualizace a opravy pro virtuální počítače Windows Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolit Diagnostika spouštění na virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazit metriky hostitele virtuálního počítače
> * Instalace rozšíření diagnostiky
> * Zobrazení metrik virtuálního počítače
> * Vytvořit výstrahu
> * Správa aktualizací Windows
> * Nastavení pokročilého monitorování

Tento kurz vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si ho můžete nechat vytvořit pomocí tohoto [ukázkového skriptu](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). Při práci se prostřednictvím tohoto kurzu je nahradit skupinu prostředků, název virtuálního počítače a umístění tam, kde je potřeba.

## <a name="view-boot-diagnostics"></a>Zobrazení diagnostiky spouštění

Jak spustit virtuální počítače s Windows, zaznamená agenta pro diagnostiku spouštěcí obrazovky výstupu, který lze použít pro účely odstraňování potíží. Tato funkce je ve výchozím nastavení povolené. Snímky obrazovky zaznamenané jsou uložené v účtu úložiště Azure, který se také vytvoří ve výchozím nastavení. 

Můžete získat spouštění diagnostiky dat pomocí [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) příkaz. V následujícím příkladu se Diagnostika spouštění stáhnou do kořenového adresáře * c:\* jednotky. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Windows má vyhrazený virtuální počítač hostitele v Azure, který komunikuje se službou. Metriky jsou automaticky shromažďovat pro hostitele a lze je zobrazit na portálu Azure.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroup** a potom v seznamu prostředků vyberte **myVM**.
2. Klikněte na tlačítko **metriky** v okně virtuálního počítače a potom vyberte některé z metriky hostitele pod **dostupné metriky** zobrazíte, jaký je výkon hostitele virtuálního počítače.

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalace diagnostického rozšíření

Metriky základní hostitele jsou dostupné, ale podrobnější a metriky specifické pro virtuální počítač, budete muset nainstalovat rozšíření diagnostiky Azure ve virtuálním počítači. Diagnostické rozšíření Azure umožňuje načítání dalších monitorovacích a diagnostických dat z virtuálního počítače. Tyto metriky výkonu můžete zobrazit a vytvářet výstrahy na základě výkonnosti virtuálního počítače. Diagnostické rozšíření se instaluje prostřednictvím portálu Azure Portal následujícím způsobem:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroup** a potom v seznamu prostředků vyberte **myVM**.
2. Klikněte na **Nastavení diagnostiky**. V seznamu je vidět, že *Diagnostika spouštění* je již povolená z předchozí části. Zaškrtněte políčko *Základní metriky*.
3. Klikněte **povolit sledování na úrovni hosta** tlačítko.

    ![Zobrazení diagnostických metrik](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Zobrazení metrik virtuálního počítače

Metriky virtuálního počítače lze zobrazit stejným způsobem jako metriky virtuálního počítače hostitele:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroup** a potom v seznamu prostředků vyberte **myVM**.
2. Pokud chcete zjistit, jaký je výkon virtuálního počítače, klikněte na tlačítko **Metriky** v okně virtuálního počítače a pak vyberte některou z diagnostických metrik v části **Dostupné metriky**.

    ![Zobrazení metrik virtuálního počítače](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroup** a potom v seznamu prostředků vyberte **myVM**.
2. Klikněte na tlačítko **Pravidla výstrah** v okně virtuálního počítače a potom na **Přidat upozornění metriky** v horní části okna výstrahy.
4. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.
5. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.
6. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.
7. Klikněte na tlačítko **OK**.

## <a name="manage-windows-updates"></a>Správa aktualizací Windows

Správa aktualizací můžete spravovat aktualizace a opravy pro virtuální počítače Windows Azure.
Přímo z virtuálního počítače můžete rychle vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích.

Informace o cenách najdete na stránce s [cenami služby Automation za správu aktualizací](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Povolení řešení Update Management

Povolení správy aktualizací pro virtuální počítač:
 
1. Na levé straně obrazovky vyberte **Virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **Operace** klikněte na **Update Management**. Otevře se obrazovka **Povolit řešení Update Management**.

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management. Toto ověření zahrnuje kontroly pracovního prostoru Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení nachází v tomto pracovním prostoru.

Pracovní prostor Log Analytics slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě. Pokud chcete na virtuálních počítačích, které vyžadují aktualizace, provádět další akce, umožňuje Azure Automation spouštět ve virtuálních počítačích skripty, například ke stažení a použití aktualizací.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Microsoft Monitoring Agent (MMA) a procesem hybrid worker. Agent slouží ke komunikaci s virtuálním počítačem a získávání informací o nainstalovaném softwaru. 

Pokud se nesplní tyto požadavky, zobrazí se banner nabízející možnost povolit dané řešení.

![Banner konfigurace připojení k řešení Update Management](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Kliknutím na banner řešení povolte. Pokud po ověření chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* Povolený [hybridní pracovní proces runbooku](../../automation/automation-hybrid-runbook-worker.md) na virtuálním počítači

Otevře se obrazovka **Povolit řešení Update Management**. Nakonfigurujte nastavení a klikněte na tlačítko **Povolit**.

![Povolení řešení Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

Povolení řešení může trvat až 15 minut a během této doby byste neměli zavírat okno prohlížeče. Po povolení řešení začnou do Log Analytics proudit informace o chybějících aktualizacích na virtuálních počítačích.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

### <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení **správy aktualizací** se zobrazí obrazovka **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

 ![Zobrazení stavu aktualizace](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu.
Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

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
> Aktualizace, které vyžadují restartování je virtuální počítač automaticky restartuje.

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

## <a name="advanced-monitoring"></a>Pokročilé sledování 

Pomocí sady [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) můžete provést rozšířené monitorování virtuálního počítače. Pokud jste to již neudělali, můžete si zaregistrovat [bezplatnou zkušební verzi](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) sady Operations Management Suite.

Až budete mít přístup k portálu OMS, najdete klíč a identifikátor pracovního prostoru v okně Nastavení. Použití [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) příkaz pro přidání rozšíření OMS k virtuálnímu počítači. Aktualizace hodnoty proměnné v níže ukázka tak, aby odrážela jste klíč pracovního prostoru OMS a prostoru ID.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Po několika minutách měli byste vidět nový virtuální počítač v pracovním prostoru OMS. 

![Okno OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali a zkontrolovat virtuálních počítačů pomocí Azure Security Center. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření skupiny prostředků a virtuálních počítačů 
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele
> * Instalace rozšíření diagnostiky
> * Zobrazení metrik virtuálního počítače
> * Vytvořit výstrahu
> * Správa aktualizací Windows
> * Nastavení pokročilého monitorování

Přechodu na v dalším kurzu se dozvíte o službě Azure security center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálních počítačů](./tutorial-azure-security.md)