---
title: Kurz – monitorování virtuálních počítačů se systémem Linux v Azure | Microsoft Docs
description: V tomto kurzu se naučíte monitorovat výkon a zjištěné součásti aplikace spuštěné na virtuálních počítačích se systémem Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680075"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Kurz: monitorování virtuálního počítače se systémem Linux v Azure

Azure Monitoring používá agenty ke shromažďování dat o spuštění a výkonu z virtuálních počítačů Azure, ukládání těchto dat do úložiště Azure a zpřístupnění přes portál, modul Azure PowerShell a Azure CLI. Rozšířené monitorování se dodává s Azure Monitor pro virtuální počítače shromažďováním metrik výkonu, zjišťováním komponent aplikace nainstalovaných na virtuálním počítači a zahrnuje grafy výkonu a mapu závislostí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazit metriky hostitele virtuálních počítačů
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazit metriky výkonu virtuálních počítačů
> * Vytvořit výstrahu

## <a name="launch-azure-cloud-shell"></a>Spustit Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.30 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Vytvořit virtuální počítač

Pokud chcete zobrazit diagnostiku a metriky v akci, potřebujete virtuální počítač. Nejdřív vytvořte skupinu prostředků pomocí [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupMonitor* v umístění *eastus* .

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Nyní vytvořte virtuální počítač pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM* a vygeneruje klíče SSH, pokud ještě neexistují v *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Povolit diagnostiku spouštění

Po spuštění virtuálních počítačů se systémem Linux zachytí spouštěcí rozšíření spouštění výstup spouštění a uloží ho do služby Azure Storage. Tato data lze použít k řešení potíží se spouštěním virtuálního počítače. Při vytváření virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku Azure se Diagnostika spouštění nepovolí automaticky.

Než povolíte diagnostiku spouštění, je potřeba vytvořit účet úložiště pro ukládání protokolů spuštění. Účty úložiště musí mít globálně jedinečný název, musí být dlouhý 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Vytvořte účet úložiště pomocí příkazu [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) . V tomto příkladu se k vytvoření jedinečného názvu účtu úložiště používá náhodný řetězec.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Při povolování diagnostiky spouštění je potřeba identifikátor URI kontejneru úložiště objektů BLOB. Následující příkaz se dotazuje na účet úložiště, který vrátí tento identifikátor URI. Hodnota identifikátoru URI je uložena v názvech proměnných *bloburi*, které se používají v dalším kroku.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Teď povolte diagnostiku spouštění pomocí [AZ VM Boot-Diagnostics Enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Hodnota `--storage` je identifikátor URI objektu BLOB shromážděného v předchozím kroku.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Zobrazit diagnostiku spouštění

Když je povolená Diagnostika spouštění, při každém zastavení a spuštění virtuálního počítače se informace o procesu spouštění zapisují do souboru protokolu. V tomto příkladu napřed nasaďte virtuální počítač pomocí příkazu [AZ VM unallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) následujícím způsobem:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Nyní spusťte virtuální počítač pomocí příkazu [AZ VM Start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) , a to takto:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Data diagnostiky spouštění pro *myVM* můžete získat pomocí příkazu [AZ VM Boot-Diagnostics Get-Boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) následujícím způsobem:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Zobrazit metriky hostitele

Virtuální počítač se systémem Linux má vyhrazeného hostitele v Azure, se kterým komunikuje. Metriky se automaticky shromažďují pro hostitele a dají se zobrazit v Azure Portal následujícím způsobem:

1. V Azure Portal vyberte **skupiny prostředků**, zvolte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .
1. Pokud chcete zjistit, jak se provádí hostitelský virtuální počítač, vyberte v okně virtuálního počítače **metriky** a pak v části **dostupné metriky**zvolte některou z metrik *[hostitel]* .

    ![Zobrazit metriky hostitele](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Povolit rozšířené monitorování

Postup povolení monitorování virtuálního počítače Azure pomocí Azure Monitor pro virtuální počítače:

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)** .

3. Na stránce **přehledy (Preview)** vyberte **vyzkoušet nyní**.

    ![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Pokud máte v rámci stejného předplatného pracovní prostor Log Analytics, v rozevíracím seznamu vyberte na stránce pro **registraci Azure monitor Insights** .  

    Seznam předchází výchozí pracovní prostor a umístění, kde je virtuální počítač nasazený v předplatném. 

    >[!NOTE]
    >Informace o vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování z virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Po povolení monitorování může být nutné počkat několik minut, než budete moci zobrazit metriky výkonu pro virtuální počítač.

![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Zobrazit metriky výkonu virtuálních počítačů

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře je virtuální počítač prováděn. Pokud chcete získat přístup z virtuálního počítače, proveďte následující kroky.

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)** .

3. Vyberte kartu **výkon** .

Tato stránka nejen zahrnuje grafy využití výkonu, ale také tabulku ukazující pro každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.

## <a name="create-alerts"></a>Vytváření výstrah

Můžete vytvářet výstrahy na základě konkrétních metrik výkonu. Výstrahy můžete použít k upozornění, když průměrné využití procesoru překročí určitou prahovou hodnotu nebo je dostupné volné místo na disku pod určitou velikostí, například. Výstrahy se zobrazují v Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat Azure Automation Runbooky nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu pro průměrné využití procesoru.

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. V okně virtuálního počítače klikněte na **pravidla výstrah** a pak v horní části okna výstrahy klikněte na **Přidat upozornění na metriku** .

3. Zadejte **název** upozornění, například *myAlertRule* .

4. Pokud chcete aktivovat upozornění, když procento procesoru překročí 1,0 po dobu pěti minut, ponechte vybrané ostatní výchozí hodnoty.

5. Volitelně můžete zaškrtnout políčko *vlastníkům e-mailu, přispěvatelům a čtenářům* poslat oznámení e-mailem. Výchozí akcí je zobrazení oznámení na portálu.

6. Klikněte na tlačítko **OK** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a prohlíželi výkon svého virtuálního počítače. Zjistili jste, jak:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazit metriky hostitele
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazit metriky virtuálních počítačů
> * Vytvořit výstrahu

V dalším kurzu se dozvíte o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálních počítačů](../../security/fundamentals/overview.md)
