---
title: Monitorování prostředku Azure pomocí Azure Monitoru
description: Zjistěte, jak shromažďovat a analyzovat data pro prostředek Azure v Azure Monitoru.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503654"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Úvodní příručka: Monitorování virtuálního počítače Azure pomocí Azure Monitoru
[Azure Monitor](../overview.md) začne shromažďovat data z virtuálních počítačů Azure v okamžiku, kdy jsou vytvořeny. V tomto rychlém startu budete mít stručný návod na data, která se automaticky shromáždí pro virtuální počítač Azure a jak zobrazit na webu Azure Portal. Pak povolíte [Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md) pro váš virtuální počítač, který umožní agentům na virtuálním počítači shromažďovat a analyzovat data z hostovaného operačního systému, včetně procesů a jejich závislostí.

Tento rychlý start předpokládá, že máte existující virtuální počítač Azure. Pokud ne, můžete vytvořit [virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) nebo vytvořit virtuální počítač s [Linuxem](../../virtual-machines/linux/quick-create-cli.md) po našich rychlých startech virtuálního počítače.

Podrobnější popis dat monitorování shromážděných z prostředků Azure najdete v [tématu Monitorování virtuálních počítačů Azure pomocí Azure Monitoru](../insights/monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Dokončete rychlý start monitorování prostředků Azure.
[Dokončete monitorování prostředku Azure pomocí Azure Monitoru](quick-monitor-azure-resource.md) a zobrazte stránku s přehledem, protokol aktivit a metriky pro virtuální počítač ve vašem předplatném. Virtuální počítače Azure shromažďují stejná data monitorování jako jakýkoli jiný prostředek Azure, ale to je jenom pro virtuální počítač hostitele. Zbytek tohoto rychlého startu se zaměří na sledování hostovaného operačního systému a jeho úloh.


## <a name="enable-azure-monitor-for-vms"></a>Povolení Azure Monitoru pro virtuální počítače
Zatímco metriky a protokoly aktivit budou shromažďovány pro hostitelský virtuální počítače, budete potřebovat agenta a některé konfigurace pro shromažďování a analýzu dat monitorování z hostovaného operačního systému a jeho úloh. Azure Monitor pro virtuální počítače nainstaluje tyto agenty a poskytuje další výkonné funkce pro monitorování vašich virtuálních počítačů.

1. Přejděte do nabídky pro váš virtuální počítač.
2. Buď klikněte na **Přejít na přehledy** z dlaždice na stránce **Přehled,** nebo klikněte na **Přehledy** z nabídky **Monitorování.**

    ![Stránka s přehledem](media/quick-monitor-azure-vm/overview-insights.png)

3. Pokud Azure Monitor pro virtuální počítače ještě nebyla povolená pro virtuální počítač, klikněte na **povolit**. 

    ![Povolení přehledů](media/quick-monitor-azure-vm/enable-insights.png)

4. Pokud virtuální počítač ještě není připojený k pracovnímu prostoru Analýzy protokolů, budete vyzváni k výběru existujícího pracovního prostoru nebo k vytvoření nového. Vyberte výchozí pracovní prostor s jedinečným názvem ve stejné oblasti jako váš virtuální počítač.

    ![Výběr pracovního prostoru](media/quick-monitor-azure-vm/select-workspace.png)

5. Registrace bude trvat několik minut, protože jsou povolena rozšíření a agenti jsou nainstalované ve vašem virtuálním počítači. Po dokončení se zobrazí zpráva, že přehledy byly úspěšně nasazeny. Kliknutím na **Azure Monitor** otevřete Azure Monitor pro virtuální počítače.

    ![Otevření Azure Monitoru](media/quick-monitor-azure-vm/azure-monitor.png)

6. Uvidíte svůj virtuální počítač s dalšími virtuálními počítači ve vašem předplatném, které jsou na palubě. Pokud chcete zobrazit virtuální počítače ve vašem předplatném, které nejsou na palubě, vyberte kartu **Nemonitorované.**

    ![Začínáme](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurace pracovního prostoru
Když vytvoříte nový pracovní prostor Analýzy protokolů, musí být nakonfigurován tak, aby shromažďoval protokoly. Tuto konfiguraci je třeba provést pouze jednou, protože konfigurace je odeslána do všech virtuálních počítačů, které se k ní připojují.

1. Vyberte **konfiguraci pracovního prostoru** a pak vyberte pracovní prostor.

2. Vybrat **upřesnit nastavení**

    ![Upřesňující nastavení Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Shromažďování dat z virtuálního počítače s Windows


2. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.

3. Přidejte protokol událostí zadáním názvu protokolu.  Zadejte **systém** a vyberte znaménko **+** plus .

4. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.

5. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

### <a name="data-collection-from-linux-vm"></a>Shromažďování dat z virtuálního počítače s Linuxem

1. Vyberte **Syslog**.  

2. Přidejte protokol událostí zadáním názvu protokolu.  Zadejte **Syslog** a vyberte znaménko **+** plus .  

3. V tabulce odznačte informace **o**závažnosti , **oznámení** a **ladění**. 

4. V horní části stránky vyberte **Uložit,** chcete-li konfiguraci uložit.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

7. Klikněte na virtuální počítač a vyberte kartu **Výkon.** To ukazuje vybranou skupinu čítačů výkonu shromážděných z hostovaného operačního systému vašeho virtuálního počítače. Posunutím dolů zobrazíte další čítače a pohybem myši nad grafem zobrazíte průměr a percentily v různých časech.

    ![Výkon](media/quick-monitor-azure-vm/performance.png)

9. Výběrem **možnosti Mapa** otevřete funkci mapy, která zobrazuje procesy spuštěné ve virtuálním počítači a jejich závislosti. Výběrem **možnosti Vlastnosti** otevřete podokno vlastností, pokud ještě není otevřené.

    ![Mapa](media/quick-monitor-azure-vm/map.png)

11. Rozšiřte procesy pro váš virtuální počítač. Vyberte jeden z procesů, chcete-li zobrazit jeho podrobnosti a zvýraznit jeho závislosti.

    ![Procesy](media/quick-monitor-azure-vm/processes.png)

12. Znovu vyberte virtuální počítač a pak **vyberte Protokolovat události**. 

    ![Protokolovat události](media/quick-monitor-azure-vm/log-events.png)

13. Zobrazí se seznam tabulek, které jsou uloženy v pracovním prostoru Log Analytics pro virtuální počítač. Tento seznam se bude lišit v závislosti na tom, zda používáte virtuální počítač se systémem Windows nebo Linux. Klikněte na tabulku **Událost.** To zahrnuje všechny události z protokolu událostí systému Windows. Log Analytics otevře s jednoduchýdotaz na číst položky protokolu událostí.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste povolili Azure Monitor pro virtuální počítače pro virtuální počítač a nakonfigurovali pracovní prostor Log Analytics pro shromažďování událostí pro hostovaný operační systém. Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
