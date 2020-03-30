---
title: Shromažďování vlastních metrik pro virtuální počítač s Linuxem s agentem InfluxData Telegraf
description: Pokyny, jak nasadit agenta InfluxData Telegraf na virtuální počítač s Linuxem v Azure a nakonfigurovat agenta tak, aby publikoval metriky do Azure Monitoru.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655459"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Shromažďování vlastních metrik pro virtuální počítač s Linuxem s agentem InfluxData Telegraf

Pomocí Azure Monitoru můžete shromažďovat vlastní metriky prostřednictvím telemetrie aplikace, agenta spuštěného na vašich prostředcích Azure nebo dokonce externích monitorovacích systémů. Pak je můžete odeslat přímo do Azure Monitoru. Tento článek obsahuje pokyny, jak nasadit [agenta InfluxData](https://www.influxdata.com/) Telegraf na virtuální počítač s Linuxem v Azure a nakonfigurovat agenta pro publikování metrik do Azure Monitoru. 

## <a name="influxdata-telegraf-agent"></a>Agent PřílivData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/) je modul plug-in-řízený agent, který umožňuje shromažďování metrik z více než 150 různých zdrojů. V závislosti na tom, jaké úlohy běží na vašem virtuálním počítači, můžete nakonfigurovat agenta tak, aby využíval specializované vstupní moduly plug-in ke shromažďování metrik. Příklady jsou MySQL, NGINX a Apache. Pomocí výstupních modulů plug-in pak může agent zapisovat do cílů, které zvolíte. Agent Telegraf se integroval přímo s vlastním rozhraním REST API azure monitoru. Podporuje výstupní modul plug-in Azure Monitor. Pomocí tohoto modulu plug-in může agent shromažďovat metriky specifické pro úlohy na vašem virtuálním počítači s Linuxem a odesílat je jako vlastní metriky do Azure Monitoru. 

 ![Telegrafní agent - přehled](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Odeslání vlastních metrik 

Pro účely tohoto kurzu nasadíme virtuální počítač s Linuxem, který běží operační systém Ubuntu 16.04 LTS. Agent Telegraf je podporován pro většinu operačních systémů Linux. Balíčky Debianu i RPM jsou k dispozici spolu s nezabalenými linuxovými binárními soubory na [portálu pro stažení InfluxData](https://portal.influxdata.com/downloads). Další pokyny a možnosti instalace naleznete v této příručce k [instalaci telegrafu.](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) 

Přihlaste se k [portálu Azure](https://portal.azure.com).

Vytvoření nového virtuálního počítače s Linuxem: 

1. V levém navigačním podokně vyberte možnost **Vytvořit prostředek.** 
1. Vyhledejte **virtuální počítač**.  
1. Vyberte **Ubuntu 16.04 LTS** a vyberte **Vytvořit**. 
1. Zadejte název virtuálního montova, jako **je MyTelegrafVM**.  
1. Ponechte typ disku jako **SSD**. Pak zadejte **uživatelské jméno**, například **azureuser**. 
1. V **části Typ ověřování**vyberte možnost **Heslo**. Pak zadejte heslo, které budete později používat na SSH do tohoto virtuálního počítače. 
1. Zvolte, zda chcete **vytvořit novou skupinu prostředků**. Potom zadejte název, například **myResourceGroup**. Zvolte svou **polohu**. Pak vyberte **OK**. 

    ![Vytvoření virtuálního počítače s Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Vyberte velikost virtuálního počítače. Můžete filtrovat například podle **Typu výpočtu** nebo **Typu disku**. 

    ![Přehled pro agenta telegrafu velikosti virtuálního počítače](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na stránce **Nastavení** ve**skupině** > zabezpečení **sítě** > **Vyberte veřejné příchozí porty**vyberte **možnost HTTP** a **SSH (22).** Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**. 

1. Na stránce Souhrn výběrem možnosti **Vytvořit** spusťte nasazení virtuálního počítače. 

1. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače. 

1. V podokně virtuálních počítače přejděte na kartu **Identita.** **On** 
 
    ![Náhled identity virtuálního virtuálního rozhraní Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači 

Vytvořte připojení SSH k virtuálnímu počítači. Na stránce Přehled pro váš virtuální počítač vyberte tlačítko **Připojit**. 

![Stránka s přehledem virtuálních zařízení Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na stránce **Připojení k virtuálnímu počítači** ponechte výchozí výběr možností pro připojení podle názvu DNS přes port 22. V **přihlašovacím objektu pomocí místního účtu virtuálního účtu**se zobrazí příkaz připojení. Vyberte tlačítko pro kopírování příkazu. Následující příklad ukazuje, jak vypadá příkaz pro připojení přes SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Vložte příkaz připojení SSH do prostředí, jako je Azure Cloud Shell nebo Bash na Ubuntu ve Windows, nebo použijte klienta SSH podle vašeho výběru k vytvoření připojení. 

## <a name="install-and-configure-telegraf"></a>Instalace a konfigurace telegrafu 

Chcete-li nainstalovat balíček Telegraf Debian do virtuálního počítače, spusťte následující příkazy z relace SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Konfigurační soubor telegrafu definuje operace telegrafu. Ve výchozím nastavení je ukázkový konfigurační soubor nainstalován na cestě **/etc/telegraf/telegraf.conf**. V ukázkovém konfiguračním souboru jsou uvedeny všechny možné vstupní a výstupní moduly plug-in. Vytvoříme však vlastní konfigurační soubor a agent ho použije spuštěním následujících příkazů: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Předchozí kód umožňuje pouze dva vstupní moduly plug-in: **cpu** a **mem**. Můžete přidat další vstupní moduly plug-in, v závislosti na zatížení, které běží na vašem počítači. Příklady jsou Docker, MySQL a NGINX. Úplný seznam vstupních modulů plug-in naleznete v části **Další konfigurace.** 

Nakonec, aby agent začal používat novou konfiguraci, vynutíme agenta zastavit a spustit spuštěním následujících příkazů: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Teď agent bude shromažďovat metriky z každého vstupního modulu plug-in zadaný a vypouštět je do Azure Monitoru. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Vykreslení metrik Telegrafu na webu Azure Portal 

1. Otevřete [portál Azure](https://portal.azure.com). 

1. Přejděte na novou kartu **Monitor.** Pak vyberte **Metriky**.  

     ![Monitor – metriky (náhled)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Vyberte virtuální počítač ve výběru prostředků.

     ![Metrický graf](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Vyberte obor názvů **Telegraf/CPU** a vyberte **metriku usage_system.** Můžete se rozhodnout filtrovat podle dimenzí této metriky nebo je rozdělit.  

     ![Výběr oboru názvů a metriky](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Další konfigurace 

Předchozí návod obsahuje informace o tom, jak nakonfigurovat agenta Telegraf pro shromažďování metrik z několika základních vstupních modulů plug-in. Agent Telegraf má podporu pro více než 150 vstupních modulů plug-in, přičemž některé podporují další možnosti konfigurace. Společnost InfluxData zveřejnila [seznam podporovaných pluginů](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) a [pokyny, jak je nakonfigurovat](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Navíc v tomto návodu jste použili agenttelegraf k vyzařování metriko v y virtuálního počítači, na který je agent nasazen. Agent Telegraf lze také použít jako kolektor a předávání metrik pro jiné prostředky. Informace o tom, jak nakonfigurovat agenta tak, aby vyzařoval metriky pro jiné prostředky Azure, najdete v [tématu Azure Monitor Custom Metric Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud už nejsou potřeba, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro virtuální počítač a vyberte **odstranit**. Potom potvrďte název skupiny prostředků, kterou chcete odstranit. 

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).



