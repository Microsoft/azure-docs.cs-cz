---
title: Kurz – Monitorování a aktualizace virtuálních počítačů s Linuxem v Azure | Microsoft Docs
description: V tomto kurzu se naučíte, jak monitorovat diagnostiku spouštění a metriky výkonu a jak spravovat aktualizace balíčků na virtuálním počítači s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c83cd869142967b358aa5d234d7d487b3c54b4c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607984"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Kurz: Monitorování a aktualizace virtuálního počítače se systémem Linux v Azure

K zajištění správného chodu virtuálních počítačů v Azure můžete zkontrolovat diagnostiku spouštění, metriky výkonu a spravovat aktualizace balíčků. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazení metrik hostitele
> * Povolení diagnostického rozšíření ve virtuálním počítači
> * Zobrazení metrik virtuálního počítače
> * Vytvoření výstrah na základě diagnostických metrik
> * Správa aktualizace balíčků
> * Monitorování změn a inventáře
> * Nastavení pokročilého monitorování

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Vytvořit virtuální počítač

Pokud chcete vidět, jak funguje diagnostika a metriky, potřebujete virtuální počítač. Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroupMonitor* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Teď pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač. Následující příklad vytvoří virtuální počítač *myVM*, a pokud klíče SSH ještě neexistují, vytvoří je v umístění *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Povolení diagnostiky spouštění

Při spouštění virtuálních počítačů s Linuxem zaznamenává diagnostické rozšíření výstup spouštění a uloží ho v úložišti Azure. Tato data můžete použít k odstraňování problémů při spouštění virtuálních počítačů. Po vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure není diagnostika spouštění automaticky povolená.

Před povolením diagnostiky spouštění je třeba vytvořit účet úložiště pro ukládání protokolů spouštění. Účty úložiště musí mít globální jedinečný název v rozmezí 3 až 24 znaků a musí obsahovat pouze čísla a malá písmena. Účet úložiště vytvoříte příkazem [az storage account create](/cli/azure/storage/account#az-storage-account-create). V tomto příkladu se k vytvoření jedinečného názvu účtu úložiště použil náhodný řetězec.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Při povolování diagnostiky spouštění je potřeba identifikátor URI pro kontejner úložiště objektů blob. Následující příkaz se dotazuje na účet úložiště za účelem vrácení tohoto identifikátoru URI. Hodnota identifikátoru URI je uložená v názvech proměnných *bloburi*, které se používají v dalším kroku.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Teď povolte diagnostiku spouštění pomocí příkazu [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Hodnota `--storage` je identifikátor URI objektu blob získaný v předchozím kroku.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Zobrazit diagnostiku spouštění

Pokud je povolená diagnostika spouštění, zapíše se při každém spuštění a vypnutí virtuálního počítače informace o procesu spouštění do souboru protokolu. V tomto příkladu nejprve zrušte přidělení virtuálního počítače příkazem [az OM deallocate](/cli/azure/vm#az-vm-deallocate) takto:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Nyní spusťte virtuální počítač pomocí příkazu [az vm start]( /cli/azure/vm#az-vm-stop) následujícím způsobem:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Data diagnostiky spouštění pro *myVM* můžete získat pomocí příkazu [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) takto:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Linuxem má vyhrazeného hostitele v Azure, který s ním komunikuje. Metriky se pro hostitele shromažďují automaticky a lze je zobrazit na portálu Azure Portal následujícím způsobem:

1. Na webu Azure Portal vyberte **Skupiny prostředků**, zvolte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
1. Pokud chcete získat informace o výkonu virtuálního počítače hostitele, vyberte v okně virtuálního počítače **Metriky** a potom v části **Dostupné metriky** vyberte některou z metrik *[hostitele]* .

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalace diagnostického rozšíření

Jsou k dispozici základní metriky hostitele, ale pokud chcete zobrazit podrobné metriky specifické pro virtuální počítač, musíte ve virtuálním počítači nainstalovat diagnostické rozšíření Azure. Diagnostické rozšíření Azure umožňuje načítání dalších monitorovacích a diagnostických dat z virtuálního počítače. Tyto metriky výkonu můžete zobrazit a vytvářet výstrahy na základě výkonnosti virtuálního počítače. Diagnostické rozšíření se instaluje prostřednictvím portálu Azure Portal následujícím způsobem:

1. Na webu Azure Portal zvolte **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
1. Vyberte **Nastavení diagnostiky**. Pokud ještě není v rozevírací nabídce *Výběr účtu úložiště* vybraný účet *mydiagdata[1234]* vytvořený v předchozí části, zvolte ho.
1. Vyberte tlačítko **Povolit monitorování na úrovni hosta**.

    ![Zobrazení diagnostických metrik](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Zobrazení metrik virtuálního počítače

Metriky virtuálního počítače lze zobrazit stejným způsobem jako metriky virtuálního počítače hostitele:

1. Na webu Azure Portal zvolte **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
1. Pokud chcete získat informace o výkonu virtuálního počítače, vyberte v okně virtuálního počítače **Metriky** a potom v části **Dostupné metriky** vyberte některou z diagnostických metrik *[hosta]* .

    ![Zobrazení metrik virtuálního počítače](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na webu Azure Portal zvolte **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Vyberte **Upozornění (klasická)** , potom zvolte **Přidat upozornění metriky (klasické)** , které se přidá do horní části okna upozornění.
3. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.
4. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.
5. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.
6. Vyberte tlačítko **OK**.

## <a name="manage-software-updates"></a>Spravovat aktualizace softwaru

Správa aktualizací umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Linuxem.
Přímo z virtuálního počítače můžete rychle vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích.

Informace o cenách najdete na stránce s [cenami služby Automation za správu aktualizací](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Povolení řešení Update Management

Povolení řešení Update Management pro virtuální počítač:

1. Na levé straně obrazovky vyberte **Virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **Operace** vyberte **Správa aktualizací**. Otevře se obrazovka **Povolit řešení Update Management**.

Provede se ověření, pomocí kterého se určí, jestli je pro tento virtuální počítač povolené řešení Update Management.
Toto ověření zahrnuje kontroly pracovního prostoru služby Log Analytics a propojeného účtu Automation a kontrolu, jestli se řešení nachází v tomto pracovním prostoru.

Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.
Pokud na virtuálních počítačích, které vyžadují aktualizace, chcete provádět další akce, Azure Automation umožňuje spouštět proti virtuálním počítačům runbooky například pro stahování a aplikování aktualizací.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Log Analytics a Automation Hybrid Runbook Worker. Agent slouží ke komunikaci s virtuálním počítačem a získávání informací o nainstalovaném softwaru.

Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Účet Automation](../../automation/automation-offering-get-started.md)
* Povolený [hybridní pracovní proces runbooku](../../automation/automation-hybrid-runbook-worker.md) na virtuálním počítači

Otevře se obrazovka řešení **Update Management**. Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a vyberte **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

![Povolení řešení Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče. Po povolení řešení budou informace o chybějících aktualizacích na virtuálním počítači Azure Monitor protokoly. Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

### <a name="view-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení **správy aktualizací** se zobrazí obrazovka **Správa aktualizací**. Po vyhodnocení aktualizací se na kartě **Chybějící aktualizace** zobrazí seznam chybějících aktualizací.

 ![Zobrazení stavu aktualizace](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Pokud chcete nainstalovat aktualizace, naplánujte nasazení odpovídající vašemu plánu vydávání a časovému intervalu pro správu a údržbu. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Naplánujte nové nasazení aktualizací pro virtuální počítač kliknutím na **Naplánovat nasazení aktualizace** v horní části obrazovky **Update Management**. Na obrazovce **Nové nasazení aktualizací** zadejte následující informace :

Pokud chcete vytvořit nové nasazení aktualizace, vyberte **naplánovat nasazení aktualizací**. Otevře se stránka **nové nasazení aktualizace** . Zadejte hodnoty vlastností popsaných v následující tabulce a pak klikněte na **vytvořit**:

| Vlastnost | Description |
| --- | --- |
| Name |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Skupiny, které se mají aktualizovat |V případě počítačů Azure definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. </br></br>V případě počítačů mimo Azure vyberte existující uložené hledání a vyberte skupinu počítačů mimo Azure, které chcete zahrnout do nasazení. </br></br>Další informace najdete v tématu [dynamické skupiny](../../automation/automation-update-management.md#using-dynamic-groups) .|
| Počítače, které se mají aktualizovat |V rozevíracím seznamu vyberte uložené hledání, importovanou skupinu nebo vyberte možnost počítač a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete.|
|Zahrnout nebo vyloučit aktualizace|Tím se otevře stránka **zahrnutí/vyloučení** . Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o způsobu zpracování zahrnutí najdete v tématu věnovaném [chování při zahrnutí](../../automation/automation-update-management.md#inclusion-behavior) . |
|Nastavení plánu|Vyberte čas, kdy se má spustit, a pro opakování vyberte buď jednou, nebo opakovanou.|
| Pre-Scripts + post-Scripts|Vyberte skripty, které se spustí před nasazením a po něm.|
| Časové období údržby |Počet minut, po které se nastaví aktualizace. Hodnota nesmí být kratší než 30 minut a maximálně 6 hodin. |
| Restartovat ovládací prvek| Určuje, jak by se mělo zpracovat restartování. Dostupné možnosti jsou:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Pouze restartovat – nenainstalují se aktualizace|

Nasazení aktualizací lze také vytvořit programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou lze použít k vytvoření týdenního nasazení aktualizací. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Jakmile dokončíte konfiguraci plánu, klikněte na tlačítko **Vytvořit** a vrátíte se na řídicí panel stavu.
Všimněte si, že v tabulce **Naplánované** se zobrazí plán nasazení, který jste vytvořili.

### <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** na obrazovce **Správa aktualizací**.
Pokud je nasazení aktuálně spuštěno, jeho stav je **Probíhající**. Po úspěšném dokončení se změní na **Úspěch**.
Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečné selhání**.
Výběrem dokončeného nasazení aktualizací zobrazíte řídicí panel pro toto nasazení aktualizací.

![Řídicí panel stavu nasazování aktualizací pro konkrétní nasazení](./media/tutorial-monitoring/manage-updates-view-results.png)

Na dlaždici **Výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači.
V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace, které můžou mít jednu z následujících hodnot:

* **Nebyl proveden pokus** – aktualizace se nenainstalovala, protože podle definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch** – Aktualizace byla úspěšná.
* **Neúspěch** – Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem dlaždice **Výstup** zobrazíte datový proud úlohy runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Výběrem možnosti **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="monitor-changes-and-inventory"></a>Monitorování změn a inventáře

Na svých počítačích můžete shromažďovat a zobrazovat inventář softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows. Sledování konfigurací vašich počítačů vám může pomoci přesně identifikovat provozní problémy napříč prostředím a lépe porozumět stavu vašich počítačů.

### <a name="enable-change-and-inventory-management"></a>Povolení správy změn a inventáře

Povolení správy změn a inventáře pro virtuální počítač:

1. Na levé straně obrazovky vyberte **Virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **Operace** vyberte **Soupis** nebo **Sledování změn**. Otevře se obrazovka **Povolit řešení Change Tracking a Inventory**.

Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a vyberte **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation. I když jsou řešení v nabídce oddělená, stále se jedná o stejné řešení. Povolením jednoho se na virtuálním počítači povolí obě.

![Povolení sledování změn a inventáře](./media/tutorial-monitoring/manage-inventory-enable.png)

Po povolení řešení může shromažďování inventáře na virtuálním počítači nějakou dobu trvat, a až pak se zobrazí data.

### <a name="track-changes"></a>Sledování změn

Na svém virtuálním počítači v části **OPERACE** vyberte **Change Tracking**. Vyberte **Upravit nastavení** a zobrazí se stránka **Sledování změn**. Vyberte typ nastavení, které chcete sledovat, a vybráním možnosti **+ Přidat** nastavení nakonfigurujte. Pro Linux je k dispozici možnost **Soubory Linuxu**.

Podrobné informace o řešení Change Tracking najdete v tématu [Řešení potíží se změnami na virtuálním počítači](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Zobrazení inventáře

Na svém virtuálním počítači v části **OPERACE** vyberte **Inventory**. Na kartě **Software** je tabulkový seznam nalezeného softwaru. V tabulce jsou zobrazené základní podrobnosti o jednotlivých záznamech softwaru. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel a čas poslední aktualizace.

![Zobrazení inventáře](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorování protokolů aktivit a změn

Na stránce **Change Tracking** na vašem virtuálním počítači vyberte **Správa připojení protokolu aktivit**. Tato úloha otevře stránku **Protokol aktivit Azure**. Vyberte **Připojit** a propojte řešení Change Tracking s protokolem aktivit Azure pro váš virtuální počítač.

Když je toto nastavení povolené, přejděte na stránku **Přehled** vašeho virtuálního počítače a výběrem **Zastavit** virtuální počítač zastavte. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Až bude přidělení vašeho virtuálního počítače zrušeno, vyberte **Spustit** a restartujte ho.

Zastavení a spuštění virtuálního počítače zapíše tuto událost do jeho protokolu aktivit Vraťte se na stránku **Change Tracking**. Vyberte **Události** v dolní části stránky. Po chvíli se události zobrazí v grafu a tabulce. Každou událost je možné vybrat a zobrazit o ní podrobné informace.

![Zobrazení změn v protokolu aktivit](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Tento graf ukazuje změny, ke kterým došlo v průběhu času. Po přidání připojení protokolu aktivit zobrazuje čára grafu úplně nahoře události protokolu aktivit Azure. Jednotlivé řádky grafu reprezentují různé typy sledovatelných změn. Těmito typy jsou linuxové procesy démon, soubory a software. Karta Změny zobrazuje podrobnosti o změnách znázorněných ve vizualizaci v sestupném pořadí podle času, kdy ke změně došlo (nejnovější je první).

## <a name="advanced-monitoring"></a>Rozšířené monitorování

Můžete provádět pokročilejší monitorování virtuálního počítače pomocí řešení, jako je [Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md), které monitoruje vaše virtuální počítače Azure ve velkém měřítku analýzou výkonu a stavu virtuálních počítačů s Windows a Linux, včetně jejich různých procesů. a propojené závislosti na dalších prostředcích a externích procesech. Správa konfigurace vašich virtuálních počítačů Azure se doručuje s využitím řešení [Azure Automation](../../automation/automation-intro.md) Change Tracking a inventáře, které umožňuje snadno identifikovat změny ve vašem prostředí. Správa dodržování předpisů aktualizace je k dispozici v řešení Azure Automation Update Management.   

Z pracovního prostoru Log Analytics, ke kterému je připojený virtuální počítač, můžete také načítat, konsolidovat a analyzovat shromážděná data pomocí [jazyka bohatých dotazů](../../azure-monitor/log-query/log-query-overview.md). 

![Pracovní prostor Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali, zkontrolovali a spravovali aktualizace pro virtuální počítač. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazení metrik hostitele
> * Povolení diagnostického rozšíření ve virtuálním počítači
> * Zobrazení metrik virtuálního počítače
> * Vytvoření výstrah na základě diagnostických metrik
> * Správa aktualizace balíčků
> * Monitorování změn a inventáře
> * Nastavení pokročilého monitorování

V dalším kurzu se dozvíte něco o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálních počítačů](../../security/fundamentals/overview.md)
