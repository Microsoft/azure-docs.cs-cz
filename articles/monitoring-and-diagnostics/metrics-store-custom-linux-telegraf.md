---
title: Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem InfluxData Telegraf
description: Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4dd9fe53e2c4198bd1b11995f00a60be3057da72
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092975"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem InfluxData Telegraf

Pomocí Azure Monitor může shromažďovat vlastní metriky prostřednictvím telemetrie vaší aplikace, agenta spuštěného na prostředky Azure, nebo dokonce i mimo se změnami systémy pro monitorování. Potom je můžete odeslat přímo do Azure monitoru. Tento článek obsahuje pokyny k nasazení [InfluxData](https://www.influxdata.com/) Telegraf agenta na virtuální počítač s Linuxem v Azure a nastavit agenta pro publikování metrik do Azure monitoru. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf agenta 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) je řízené plug-v agent, který umožňuje shromažďování metrik z více než 150 různých zdrojů. V závislosti na tom, co úlohy spusťte na svém virtuálním počítači můžete nakonfigurovat agenta využívat specializované vstupní moduly plug-in pro shromažďování metrik. Příklady jsou Apache, MySQL a server NGINX. Když použijete výstupní moduly plug-in, můžete agenta pak zapisovat do cíle, které zvolíte. Telegraf agent obsahuje integrované přímo vlastních metrik Azure monitoru rozhraní REST API. Podporuje modul plug-in Azure Monitor výstup. Prostřednictvím tohoto modulu plug-in, agenta můžete shromažďovat metriky specifické úlohy na virtuálním počítačům s Linuxem a odeslat je jako vlastní metriky do Azure monitoru. 

 ![Přehled agenta měsíci](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Odeslat vlastní metriky 

Pro účely tohoto kurzu jsme nasadit virtuální počítač s Linuxem, na kterém běží Ubuntu 16.04 LTS operačního systému. Telegraf agent se podporuje pro většinu operačních systémů Linux. Jsou k dispozici společně s nezabalené binární soubory Linuxu na balíčky Debian a ot. / min [portál pro stažení InfluxData](https://portal.influxdata.com/downloads). Najdete v tomto [Průvodce instalací Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) další pokyny k instalaci a možnosti. 

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

Vytvořte nový virtuální počítač s Linuxem: 

1. Vyberte **vytvořit prostředek** možnost v levém navigačním podokně. 
1. Vyhledejte **virtuálního počítače**.  
1. Vyberte **Ubuntu 16.04 LTS** a vyberte **vytvořit**. 
1. Zadejte název virtuálního počítače jako **MyTelegrafVM**.  
1. Ponechat typ disku jako **SSD**. Potom zadejte **uživatelské jméno**, jako například **azureuser**. 
1. Pro **typ ověřování**vyberte **heslo**. Zadejte heslo, které použijete později k SSH do tohoto virtuálního počítače. 
1. Zvolit **vytvořit novou skupinu prostředků**. Zadejte název, jako třeba **myResourceGroup**. Zvolte vaši **umístění**. Potom vyberte **OK**. 

    ![Vytvoření virtuálního počítače s Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Vyberte velikost virtuálního počítače. Můžete filtrovat podle **typ výpočtu** nebo **typ disku**, např. 

    ![Přehled agenta měsíci velikosti virtuálních počítačů](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Na **nastavení** stránku **sítě** > **skupinu zabezpečení sítě**   >  ** Vyberte veřejné příchozí porty**vyberte **HTTP** a **SSH (22)**. Ponechejte zbývající výchozí hodnoty a vyberte **OK**. 

1. Na stránce shrnutí vybrat **vytvořit** a spusťte nasazování virtuálního počítače. 

1. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení automaticky otevře souhrn virtuálního počítače. 

1. V podokně virtuální počítač, přejděte **Identity** kartu. Ujistěte se, že váš virtuální počítač má systém přiřadil identitu nastavena na **na**. 
 
    ![Virtuální počítač telegraf identity ve verzi preview](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači 

Vytvořte připojení SSH k virtuálnímu počítači. Vyberte **připojit** tlačítko na stránce Přehled pro váš virtuální počítač. 

![Stránka s přehledem telegraf virtuálního počítače](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

V **připojit k virtuálnímu počítači** stránce, ponechte výchozí nastavení pro připojení s názvem DNS přes port 22. V **přihlásit se pomocí místního účtu virtuálního počítače**, se zobrazí příkaz připojení. Vyberte tlačítko si zkopírovat příkaz. Následující příklad ukazuje, jak vypadá příkaz pro připojení přes SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Vložte připojení příkazu SSH do prostředí, jako jsou Azure Cloud Shell nebo prostředí Bash na Ubuntu ve Windows, nebo pomocí klienta SSH podle vašeho výběru vytvořte připojení. 

## <a name="install-and-configure-telegraf"></a>Instalace a konfigurace Telegraf 

Chcete-li nainstalovat balíček Debian Telegraf do virtuálního počítače, spusťte následující příkazy z relace SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf na konfigurační soubor definuje Telegraf jeho operace. Ve výchozím nastavení, je nainstalována příklad konfiguračního souboru v cestě **/etc/telegraf/telegraf.conf**. Příklad konfiguračního souboru obsahuje seznam všech možných vstupní a výstupní moduly plug-in. Však vytvoříme vlastní konfigurační soubor a agentem pomocí následujících příkazů: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Předchozí kód povoluje jen dva vstupní moduly plug-in: **procesoru** a **paměť**. Můžete přidat více vstupních modulů plug-in, v závislosti na zatížení, která běží na vašem počítači. Příklady jsou Docker, MySQL a NGINXU. Úplný seznam vstupních moduly plug-in, najdete v článku **další konfiguraci** oddílu. 

A konečně pokud chcete, aby agent začít používat novou konfiguraci, jsme vynutit agenta a spouštění spuštěním následujících příkazů: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agent bude nyní shromažďovat metriky ze všech vstupních moduly plug-in zadaný a posílat do Azure monitoru. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Vykreslení Telegraf metrik na webu Azure Portal 

1. Otevřete web [Azure Portal](https://portal.azure.com). 

1. Přejděte k novému **monitorování** kartu. Potom vyberte **metriky**.  

     ![Monitorování – metriky (preview)](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Vyberte svůj virtuální počítač v modulu pro výběr prostředků.

     ![Graf metrik](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Vyberte **Telegraf/procesoru** obor názvů a vyberte **usage_system** metriku. Můžete filtrovat podle dimenzí na tuto metriku nebo rozdělení na ně.  

     ![Vyberte obor názvů a metriky](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Další konfigurace 

Předchozí názorný postup obsahuje informace o tom, jak nakonfigurovat agenta Telegraf shromažďovat metriky z několika základní vstupní moduly plug-in. Telegraf agenta obsahuje podporu pro víc než 150 vstupní moduly plug-in, se některé podpůrné další možnosti konfigurace. InfluxData publikoval [seznam podporovaných modulů plug-in](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) a pokyny, [způsob jejich konfigurace](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Navíc v tomto podrobném návodu, můžete použít Telegraf agenta ke generování metriky o agenta je nasazený na virtuálním počítači. Telegraf agent také slouží jako kolekce a server pro předávání metrik za další prostředky. Další informace o konfiguraci agenta ke generování metriky pro ostatní prostředky Azure, najdete v článku [Azure Monitor vlastní metrika výstupu Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud jste už nepotřebujete, můžete odstranit skupinu prostředků, virtuálního počítače a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač a vyberte **odstranit**. Potvrďte název skupiny prostředků pro odstranění. 

## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).


