---
title: Kurz – Správa konfigurace virtuálního počítače se systémem Linux v Azure
description: V tomto kurzu zjistíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači se systémem Linux.
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
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 31a8457b4b1ac069cafbfd9713f15fdad7142d10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87445798"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Kurz: monitorování změn a aktualizace virtuálního počítače se systémem Linux v Azure

Azure [Change Tracking](../../automation/change-tracking.md) umožňuje snadno identifikovat změny a [Update Management](../../automation/update-management/update-mgmt-overview.md) vám umožní spravovat aktualizace operačního systému pro virtuální počítače Azure Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Správa aktualizací pro Linux
> * Monitorování změn a inventáře

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Vytvoření virtuálního počítače

Pokud chcete vidět, jak funguje diagnostika a metriky, potřebujete virtuální počítač. Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroupMonitor* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Teď pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač. Následující příklad vytvoří virtuální počítač *myVM*, a pokud klíče SSH ještě neexistují, vytvoří je v umístění *~/.ssh/*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Správa aktualizací softwaru

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

Pracovní prostor [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Update Management.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.
Pokud na virtuálních počítačích, které vyžadují aktualizace, chcete provádět další akce, Azure Automation umožňuje spouštět proti virtuálním počítačům runbooky například pro stahování a aplikování aktualizací.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Log Analytics a Automation Hybrid Runbook Worker. Agent slouží ke komunikaci s virtuálním počítačem a získávání informací o nainstalovaném softwaru.

Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

Pokud během připojování chyběla některá z následujících požadovaných součástí, automaticky se přidá:

* Pracovní prostor [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)
* [Účet služby Automation](../../automation/index.yml)
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

| Vlastnost | Popis |
| --- | --- |
| Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Skupiny, které se mají aktualizovat |V případě počítačů Azure definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. </br></br>V případě počítačů mimo Azure vyberte existující uložené hledání a vyberte skupinu počítačů mimo Azure, které chcete zahrnout do nasazení. </br></br>Další informace najdete v tématu [dynamické skupiny](../../automation/update-management/update-mgmt-groups.md) .|
| Počítače, které se mají aktualizovat |V rozevíracím seznamu vyberte uložené hledání, importovanou skupinu nebo vyberte možnost počítač a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|Update classifications|Vyberte všechny klasifikace aktualizací, které potřebujete.|
|Zahrnout nebo vyloučit aktualizace|Tím se otevře stránka **zahrnutí/vyloučení** . Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o způsobu zpracování zahrnutí najdete v tématu [Naplánování nasazení aktualizace](../../automation/update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment) . |
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
* **Úspěch** – aktualizace byla úspěšná.
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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zkontrolovali Change Tracking a Update Management pro svůj virtuální počítač. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Správa aktualizací pro Linux
> * Monitorování změn a inventáře

Přejděte k dalšímu kurzu, kde se dozvíte o monitorování virtuálního počítače.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitor.md)
