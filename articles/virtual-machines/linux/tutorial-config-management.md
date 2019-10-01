---
title: Kurz – Správa konfigurace virtuálního počítače se systémem Linux v Azure | Microsoft Docs
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
ms.openlocfilehash: 78c924ebe15186025f4f2a79f87be6fb4fbf5db9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680047"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Kurz: monitorování změn a aktualizace virtuálního počítače se systémem Linux v Azure

Azure [Change Tracking](../../automation/change-tracking.md) umožňuje snadno identifikovat změny a [Update Management](../../automation/automation-update-management.md) vám umožní spravovat aktualizace operačního systému pro virtuální počítače Azure Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Správa aktualizací Windows
> * Sledování změn a inventáře

## <a name="launch-azure-cloud-shell"></a>Spustit Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.30 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Vytvořit virtuální počítač

Pokud chcete zobrazit diagnostiku a metriky v akci, potřebujete virtuální počítač. Nejdřív vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupMonitor* v umístění *eastus* .

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Nyní vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM* a vygeneruje klíče SSH, pokud ještě neexistují v *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Spravovat aktualizace softwaru

Správa aktualizací umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure Linux.
Přímo z virtuálního počítače můžete rychle vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení, abyste ověřili, že se aktualizace úspěšně nastavily na virtuální počítač.

Informace o cenách najdete v tématu [ceny služby Automation pro správu aktualizací](https://azure.microsoft.com/pricing/details/automation/) .

### <a name="enable-update-management"></a>Povolit správu aktualizací

Povolení správy aktualizací pro váš virtuální počítač:

1. Na levé straně obrazovky vyberte **virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **operace** vyberte **Správa aktualizací**. Otevře se obrazovka **povolit Update Management** .

Provede se ověření, aby se zjistilo, jestli je pro tento virtuální počítač povolená Správa aktualizací.
Ověření zahrnuje kontrolu Log Analyticsho pracovního prostoru a propojeného účtu Automation, a pokud je řešení v pracovním prostoru.

[Log Analytics](../../log-analytics/log-analytics-overview.md) pracovní prostor se používá ke shromažďování dat generovaných funkcemi a službami, jako je například správa aktualizací.
Pracovní prostor poskytuje jedno umístění pro kontrolu a analýzu dat z více zdrojů.
K provedení dalších akcí na virtuálních počítačích, které vyžadují aktualizace, Azure Automation umožňuje spouštět Runbooky na virtuálních počítačích, například stáhnout a použít aktualizace.

Proces ověřování také zkontroluje, jestli je virtuální počítač zřízený s agentem Log Analytics a Automation Hybrid Runbook Worker. Tento agent slouží ke komunikaci s virtuálním počítačem a získávání informací o stavu aktualizace.

Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá až 15 minut.

Pokud se zjistilo, že během připojování chybí kterákoli z následujících předpokladů, automaticky se přidají:

* Pracovní prostor [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Účet služby Automation](../../automation/automation-offering-get-started.md)
* Na virtuálním počítači je povolený [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) .

Otevře se obrazovka **Update Management** . Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a vyberte **Povolit**. Pokud jsou pole šedá, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

![Povolit řešení Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

Povolení řešení může trvat až 15 minut. Během této doby byste neměli okno prohlížeče zavřít. Po povolení řešení budou informace o chybějících aktualizacích na virtuálním počítači Azure Monitor protokoly. Může trvat 30 minut a 6 hodin, než se data budou k dispozici pro analýzu.

### <a name="view-update-assessment"></a>Zobrazit posouzení aktualizací

Po povolení **správy aktualizací** se zobrazí obrazovka **Správa aktualizací** . Po dokončení vyhodnocení aktualizací se na kartě **chybějící aktualizace** zobrazí seznam chybějících aktualizací.

 ![Zobrazit stav aktualizace](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

Chcete-li nainstalovat aktualizace, naplánujte nasazení, které následuje po plánu vydání a okně služby. Můžete zvolit typy aktualizací, které chcete zahrnout do nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

Naplánujte nové nasazení aktualizací pro virtuální počítač kliknutím na **naplánovat nasazení aktualizací** v horní části obrazovky **Update Management** . Na obrazovce **nové nasazení aktualizací** zadejte následující informace:

Pokud chcete vytvořit nové nasazení aktualizace, vyberte **naplánovat nasazení aktualizací**. Otevře se stránka **nové nasazení aktualizace** . Zadejte hodnoty vlastností popsaných v následující tabulce a pak klikněte na **vytvořit**:

| Vlastnost | Popis |
| --- | --- |
| Name |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Skupiny, které se mají aktualizovat |V případě počítačů Azure definujte dotaz založený na kombinaci předplatného, skupin prostředků, umístění a značek, abyste vytvořili dynamickou skupinu virtuálních počítačů Azure, které chcete zahrnout do nasazení. </br></br>V případě počítačů mimo Azure vyberte existující uložené hledání a vyberte skupinu počítačů mimo Azure, které chcete zahrnout do nasazení. </br></br>Další informace najdete v tématu [dynamické skupiny](../../automation/automation-update-management.md#using-dynamic-groups) .|
| Počítače, které se mají aktualizovat |V rozevíracím seznamu vyberte uložené hledání, importovanou skupinu nebo vyberte možnost počítač a vyberte jednotlivé počítače. Pokud zvolíte možnost **počítače**, připravenost počítače se zobrazí ve sloupci **připravenosti agenta aktualizace** .</br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete.|
|Zahrnout nebo vyloučit aktualizace|Tím se otevře stránka **zahrnutí/vyloučení** . Aktualizace, které mají být zahrnuty nebo vyloučeny, jsou na různých kartách. Další informace o způsobu zpracování zahrnutí najdete v tématu věnovaném [chování při zahrnutí](../../automation/automation-update-management.md#inclusion-behavior) . |
|Nastavení plánu|Vyberte čas, kdy se má spustit, a pro opakování vyberte buď jednou, nebo opakovanou.|
| Pre-Scripts + post-Scripts|Vyberte skripty, které se spustí před nasazením a po něm.|
| Časové období údržby |Počet minut, po které se nastaví aktualizace. Hodnota nesmí být kratší než 30 minut a maximálně 6 hodin. |
| Restartovat ovládací prvek| Určuje, jak by se mělo zpracovat restartování. K dispozici jsou tyto možnosti:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Jenom restartovat – aktualizace se neinstalují.|

Nasazení aktualizací lze také vytvořit programově. Informace o tom, jak vytvořit nasazení aktualizace pomocí REST API, najdete v tématu [Konfigurace aktualizací softwaru-vytvořit](/rest/api/automation/softwareupdateconfigurations/create). K dispozici je také Ukázková sada Runbook, kterou lze použít k vytvoření týdenního nasazení aktualizací. Další informace o této sadě Runbook najdete v tématu [Vytvoření týdenního nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Po dokončení konfigurace plánu klikněte na tlačítko **vytvořit** a vrátíte se na řídicí panel stavu.
Všimněte si, že v **naplánované** tabulce se zobrazuje plán nasazení, který jste vytvořili.

### <a name="view-results-of-an-update-deployment"></a>Zobrazit výsledky nasazení aktualizace

Po spuštění naplánovaného nasazení uvidíte stav tohoto nasazení na kartě **nasazení aktualizací** na obrazovce **Správa aktualizací** .
Pokud je **v**tuto chvíli spuštěný, zobrazuje se stav probíhá. Po úspěšném dokončení se změní na **úspěch**.
Pokud dojde k selhání s jednou nebo více aktualizacemi v nasazení, stav je **částečně neúspěšný**.
Výběrem dokončeného nasazení aktualizace zobrazíte řídicí panel pro toto nasazení aktualizace.

![Řídicí panel stavu nasazení aktualizace pro konkrétní nasazení](./media/tutorial-monitoring/manage-updates-view-results.png)

Na dlaždici **výsledky aktualizací** je souhrn celkového počtu aktualizací a výsledků nasazení na virtuálním počítači.
V tabulce vpravo je podrobný rozpis každé aktualizace a výsledků instalace, což může být jedna z následujících hodnot:

* **Neproběhl pokus** – aktualizace se nenainstalovala, protože na základě definovaného trvání časového období údržby nebylo k dispozici dostatek času.
* **Úspěch** – aktualizace byla úspěšná.
* **Selhání** – aktualizace se nezdařila

Výběrem **všechny protokoly** zobrazíte všechny položky protokolu, které nasazení vytvořilo.

Výběrem dlaždice **výstup** zobrazíte datový proud úlohy Runbooku zodpovědného za správu nasazení aktualizací na cílovém virtuálním počítači.

Kliknutím na **chyby** zobrazíte podrobné informace o případných chybách nasazení.

## <a name="monitor-changes-and-inventory"></a>Sledování změn a inventáře

Na svých počítačích můžete shromažďovat a zobrazovat inventář softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows. Sledování konfigurací vašich počítačů vám pomůže určit provozní problémy v celém prostředí a lépe pochopit stav počítačů.

### <a name="enable-change-and-inventory-management"></a>Povolení správy změn a inventáře

Povolení správy změn a inventáře pro virtuální počítač:

1. Na levé straně obrazovky vyberte **virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na obrazovce virtuálního počítače v části **operace** vyberte **inventarizace** nebo **sledování změn**. Otevře se obrazovka **povolit Change Tracking a inventář** .

Nakonfigurujte umístění, Log Analytics pracovní prostor a účet Automation, které se mají použít, a vyberte **Povolit**. Pokud jsou pole šedá, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation. I když jsou řešení oddělená v nabídce, jedná se o stejné řešení. Povolením jedné možnosti povolíte obojí pro virtuální počítač.

![Povolit sledování změn a inventáře](./media/tutorial-monitoring/manage-inventory-enable.png)

Po povolení řešení může trvat nějakou dobu, než se na virtuálním počítači shromáždí inventář před zobrazením dat.

### <a name="track-changes"></a>Sledovat změny

Na svém VIRTUÁLNÍm počítači vyberte **Change Tracking** v části **operace**. Vyberte **Upravit nastavení**, zobrazí se stránka **Change Tracking** . Vyberte typ nastavení, které chcete sledovat, a pak vyberte **+ Přidat** a nakonfigurujte nastavení. Dostupná možnost Linux je Linux **Files** .

Podrobné informace o Change Tracking najdete v tématu [řešení potíží se změnami na virtuálním počítači](../../automation/automation-tutorial-troubleshoot-changes.md) .

### <a name="view-inventory"></a>Zobrazit inventář

Na svém VIRTUÁLNÍm počítači vyberte **inventarizace** v části **operace**. Na kartě **software** obsahuje tabulka seznam softwaru, který byl nalezen. Podrobnosti vysoké úrovně pro každý záznam softwaru jsou v tabulce viditelné. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel, čas poslední aktualizace.

![Zobrazit inventář](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorování protokolů aktivit a změn

Na stránce **Change Tracking (sledování změn** ) na virtuálním počítači vyberte **Spravovat připojení protokolu aktivit**. Tato úloha otevře stránku **protokolu aktivit Azure** . Vyberte **připojit** a připojte sledování změn k protokolu aktivit Azure pro váš virtuální počítač.

Když je toto nastavení povolené, přejděte na stránku **Přehled** pro váš virtuální počítač a výběrem **zastavit** zastavte virtuální počítač. Po zobrazení výzvy vyberte **Ano** , aby se virtuální počítač zastavil. Když je uvolněný, vyberte **Spustit** a restartujte virtuální počítač.

Zastavení a spuštění virtuálního počítače zapíše událost do protokolu aktivit. Přejděte zpět na stránku **Change Tracking (sledování změn** ). V dolní části stránky vyberte kartu **události** . Po chvíli se události zobrazují v grafu a tabulce. Každou událost je možné vybrat k zobrazení podrobných informací o události.

![Zobrazit změny v protokolu aktivit](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Graf zobrazuje změny, ke kterým došlo v průběhu času. Po přidání připojení protokolu aktivit zobrazuje spojnicový graf v horní části události protokolu aktivit Azure. Každý řádek pruhových grafů představuje jiný typ změny, který se může sledovat. Mezi tyto typy patří procesy démonů systému Linux, soubory a software. Karta Change (změna) zobrazuje podrobnosti o změnách zobrazených ve vizualizaci v sestupném pořadí podle času, kdy došlo ke změně (nejnovější první).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a zkontrolovali Change Tracking a Update Management pro svůj virtuální počítač. Zjistili jste, jak:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Správa aktualizací pro Linux
> * Sledování změn a inventáře

Přejděte k dalšímu kurzu, kde se dozvíte o monitorování virtuálního počítače.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitor.md)