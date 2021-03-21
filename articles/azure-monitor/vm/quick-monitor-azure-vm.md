---
title: Monitorování virtuálního počítače Azure pomocí Azure Monitor
description: Naučte se shromažďovat a analyzovat data pro virtuální počítač Azure v Azure Monitor.
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046920"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Rychlý Start: monitorování virtuálního počítače Azure pomocí Azure Monitor
[Azure monitor](../overview.md) spustí shromažďování dat z virtuálních počítačů Azure a okamžik jejich vytvoření. V tomto rychlém startu se dozvíte, jak se budou automaticky shromažďovat data pro virtuální počítač Azure a jak je zobrazit v Azure Portal. Pak pro virtuální počítač povolíte [přehledy pro virtuální](../vm/vminsights-overview.md) počítače, které umožní agentům na virtuálním počítači shromažďovat a analyzovat data z hostovaného operačního systému, včetně procesů a jejich závislostí.

Tento rychlý start předpokládá, že máte existující virtuální počítač Azure. Pokud nemůžete vytvořit [virtuální počítač s Windows](../../virtual-machines/windows/quick-create-portal.md) nebo vytvořit [virtuální počítač](../../virtual-machines/linux/quick-create-cli.md) se systémem Linux, postupujte podle našich rychlých startů pro virtuální počítače.

Podrobnější popis monitorování dat shromážděných z prostředků Azure najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](./monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Dokončete monitorování v rychlém startu prostředků Azure.
Kompletní [monitorování prostředku Azure pomocí Azure monitor](../essentials/quick-monitor-azure-resource.md) k zobrazení stránky s přehledem, protokolu aktivit a metrik pro virtuální počítač v rámci předplatného. Virtuální počítače Azure shromažďují stejná data monitorování jako všechny ostatní prostředky Azure, ale toto jsou jenom pro virtuální počítač hostitele. Zbytek tohoto rychlého startu se soustředí na monitorování hostovaného operačního systému a jeho úloh.


## <a name="enable-vm-insights"></a>Povolit přehledy virtuálních počítačů
I když budou metriky a protokoly aktivit shromažďovány pro virtuální počítač hostitele, potřebujete agenta a určitou konfiguraci pro shromažďování a analýzu dat monitorování z hostovaného operačního systému a jeho úloh. Služby VM Insights tyto agenty instalují a poskytují další výkonné funkce pro monitorování virtuálních počítačů.

1. Přejděte do nabídky pro váš virtuální počítač.
2. Buď klikněte na **Přejít na přehledy** na dlaždici na stránce **Přehled** , nebo klikněte na **přehledy** v nabídce **monitorování** .

    ![Stránka přehledu](media/quick-monitor-azure-vm/overview-insights.png)

3. Pokud se pro virtuální počítač ještě nepovolilo přehledy virtuálních počítačů, klikněte na **Povolit**. 

    ![Povolit přehledy](media/quick-monitor-azure-vm/enable-insights.png)

4. Pokud virtuální počítač ještě není připojený k pracovnímu prostoru Log Analytics, zobrazí se výzva k výběru existujícího pracovního prostoru nebo vytvoření nového. Vyberte výchozí, což je pracovní prostor s jedinečným názvem ve stejné oblasti, ve které je váš virtuální počítač.

    ![Výběr pracovního prostoru](media/quick-monitor-azure-vm/select-workspace.png)

5. Registrace bude trvat několik minut, než se povolí rozšíření a na virtuálním počítači se nainstalují agenti. Až se dokončí, dostanete zprávu, že přehledy se úspěšně nasadily. Kliknutím na **Azure monitor** otevřete službu VM Insights.

    ![Otevřít Azure Monitor](media/quick-monitor-azure-vm/azure-monitor.png)

6. Ve vašem předplatném se zobrazí váš virtuální počítač s dalšími virtuálními počítači. Pokud chcete zobrazit virtuální počítače v předplatném, které nejsou připojené, vyberte kartu **nemonitorované** .

    ![Začínáme](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurace pracovního prostoru
Když vytváříte nový pracovní prostor Log Analytics, musí být nakonfigurovaný pro shromažďování protokolů. Tato konfigurace se musí provést jenom jednou, protože se konfigurace pošle na všechny virtuální počítače, které se k němu připojují.

1. Vyberte **Konfigurace pracovního prostoru** a pak vyberte svůj pracovní prostor.

2. Vybrat **upřesňující nastavení**

    ![Upřesňující nastavení Log Analytics](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Shromažďování dat z virtuálního počítače s Windows


2. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.

3. Přidejte protokol událostí zadáním názvu protokolu.  Zadejte **systém** a potom vyberte znaménko plus **+** .

4. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.

5. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

### <a name="data-collection-from-linux-vm"></a>Shromažďování dat z virtuálního počítače s Linuxem

1. Vyberte **data** a pak vyberte **syslog**.

2. Přidejte protokol událostí zadáním názvu protokolu.  Zadejte **SYSLOG** a potom vyberte znaménko plus **+** .  

3. V tabulce zrušte výběr **informací o** závažnosti, **Upozornění** a **ladění**. 

4. V horní části stránky vyberte **Uložit** a uložte konfiguraci.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

7. Klikněte na virtuální počítač a pak vyberte kartu **výkon** , která se nachází na dlaždici monitorování **přehledů** v nabídce **monitorování** . Tím se zobrazí vybraná skupina čítačů výkonu shromážděných z hostovaného operačního systému virtuálního počítače. Posuňte se dolů a zobrazte další čítače a přesuňte ukazatel myši na graf, abyste zobrazili průměrnou a percentily v různou dobu.

    ![Snímek obrazovky se zobrazí v podokně výkon.](media/quick-monitor-azure-vm/performance.png)

9. Výběrem **mapování** otevřete funkci Maps, která zobrazuje procesy běžící na virtuálním počítači a jejich závislosti. Výběrem **vlastnosti** otevřete podokno vlastností, pokud již není otevřeno.

    ![Snímek obrazovky se zobrazí v podokně Mapa.](media/quick-monitor-azure-vm/map.png)

11. Rozbalíte procesy pro svůj virtuální počítač. Výběrem jednoho z procesů zobrazíte jeho podrobnosti a zvýrazníte jeho závislosti.

    ![Snímek obrazovky se zobrazí podokno mapa s procesy pro virtuální počítač rozbalený.](media/quick-monitor-azure-vm/processes.png)

12. Vyberte virtuální počítač znovu a pak vyberte **události protokolu**. 

    ![Události protokolu](media/quick-monitor-azure-vm/log-events.png)

13. Zobrazí se seznam tabulek, které jsou uložené v pracovním prostoru Log Analytics pro virtuální počítač. Tento seznam se liší v závislosti na tom, zda používáte virtuální počítač se systémem Windows nebo Linux. Klikněte na tabulku **událostí** . To zahrnuje všechny události z protokolu událostí systému Windows. Log Analytics se otevře s jednoduchým dotazem, který načte položky protokolu událostí.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste povolili přehledy virtuálních počítačů pro virtuální počítač a nakonfigurovali Log Analytics pracovní prostor pro shromažďování událostí pro hostovaný operační systém. Pokud chcete zjistit, jak zobrazit a analyzovat data, pokračujte k následujícímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení nebo analýza dat v Log Analytics](../logs/log-analytics-tutorial.md)