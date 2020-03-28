---
title: Výuka – monitorování virtuálních počítačů s Windows v Azure
description: V tomto kurzu se dozvíte, jak sledovat výkon a zjištěné součásti aplikací spuštěné na virtuálních počítačích windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74112487"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Kurz: Monitorování virtuálního počítače s Windows v Azure

Azure monitoring používá agenty ke shromažďování spouštěcích a výkonnostních dat z virtuálních klíčů Azure, ukládat tato data do úložiště Azure a zpřístupnit je prostřednictvím portálu, modulu Azure PowerShell a rozhraní příkazového příkazového příkazu Azure. Pokročilé monitorování se dodává s Azure Monitor pro virtuální počítače shromažďováním metrik výkonu, zjišťováním součástí aplikací nainstalovaných na virtuálním počítači a zahrnuje grafy výkonu a mapování závislostí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele virtuálního počítače
> * Povolení Azure Monitoru pro virtuální počítače
> * Zobrazení metrik výkonu virtuálních visu
> * Vytvoření upozornění

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Ke konfiguraci monitorování a správy aktualizací Azure v tomto kurzu budete potřebovat virtuální počítač s Windows v Azure. Nejdřív pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte virtuální hod s [Novým AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupMonitorMonitor* a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="view-boot-diagnostics"></a>Zobrazení diagnostiky spouštění

Při spouštění virtuálních počítačů s Windows zachytí agent diagnostiky spouštění výstup na obrazovce, který můžete použít pro účely řešení potíží. Tato funkce je ve výchozím nastavení zapnuta. Zachycené snímky obrazovky se ukládají v účtu úložiště Azure, který se také vytvoří ve výchozím nastavení.

Diagnostická data spouštění můžete získat pomocí příkazu [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). V následujícím příkladu se diagnostika spouštění stáhne do kořenové složky na jednotce *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Windows má vyhrazený virtuální počítač hostitele v Azure, který s ním komunikuje. Metriky se pro hostitele shromažďují automaticky a lze je zobrazit na portálu Azure Portal.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Pokud chcete zjistit, jaký je výkon virtuálního počítače hostitele, klikněte na **Metriky** v okně virtuálního počítače a potom vyberte některou z metrik hostitele v části **Dostupné metriky**.

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Povolit pokročilé monitorování

Povolení monitorování virtuálního počítače Azure pomocí Azure Monitoru pro virtuální počítače:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce Virtuální počítač včásti **Monitorování** vyberte **Přehledy (náhled).**

3. Na stránce **Přehledy (náhled)** vyberte **Vyzkoušet .**

    ![Povolení Azure Monitoru pro virtuální počítače pro virtuální počítač](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Na stránce **Registrace přehledů monitorování Azure,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném, vyberte ho v rozevíracím seznamu.  

    Seznam předem vybere výchozí pracovní prostor a umístění, kde se virtuální jazyk nasadí v předplatném. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z virtuálního aplikace, přečtěte [si tématu Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Po povolení monitorování možná budete muset počkat několik minut, než budete moct zobrazit metriky výkonu pro virtuální hod.

![Povolení zpracování nasazení monitorování Azure Monitor pro virtuální počítače](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Zobrazení metrik výkonu virtuálních visu

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KU), které vám pomohou určit, jak dobře si virtuální počítač vede. Chcete-li získat přístup z virtuálního počítače, proveďte následující kroky.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce Virtuální počítač včásti **Monitorování** vyberte **Přehledy (náhled).**

3. Vyberte kartu **Výkon.**

Tato stránka obsahuje nejen grafy využití výkonu, ale také tabulku zobrazující každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Klikněte na tlačítko **Pravidla výstrah** v okně virtuálního počítače a potom na **Přidat upozornění metriky** v horní části okna výstrahy.

3. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.

4. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.

5. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.

6. Klikněte na tlačítko **OK.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zobrazili výkon virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele
> * Povolení Azure Monitoru pro virtuální počítače
> * Zobrazení metrik virtuálního počítače
> * Vytvoření upozornění

V dalším kurzu se dozvíte něco o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálního počítače](../../security/fundamentals/overview.md)
