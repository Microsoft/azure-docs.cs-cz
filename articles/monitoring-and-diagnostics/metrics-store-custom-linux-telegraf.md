---
title: Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem Telegraf InfluxData
description: Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem Telegraf InfluxData
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990701"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Shromažďovat vlastní metriky pro Linuxové virtuální počítače s agentem Telegraf InfluxData

Azure Monitor umožňuje shromažďovat vlastní metriky pomocí telemetrie vaší aplikace, agenta spuštěného na prostředky Azure, nebo dokonce i mimo se změnami systémy pro monitorování a odeslat přímo do Azure monitoru. Tento článek se týká uvádějí pokyny, jak nasadit [InfluxData](https://www.influxdata.com/) Telegraf agenta na virtuální počítač s Linuxem v Azure a nastavit agenta pro publikování metrik do Azure monitoru. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf agenta 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) je agent řízené modulu plug-in, který umožňuje shromažďování metrik z více než 150 různých zdrojů. Podle toho, jaké úlohy jsou spuštěné na virtuálním počítači (např.) MySQL serveru NGINX, Apache, atd.) můžete nakonfigurovat agenta využívat specializované vstupní moduly plug-in pro shromažďování metrik. Výstupní moduly plug-in a potom povolit agenta pro zápis do cíle, které si vyberete. Telegraf agent integruje přímo vlastní metriky rozhraní REST API služby Azure Monitor a podporuje "modul plug-in Azure Monitor výstupu". To umožňuje agenta shromažďovat konkrétní metriky na virtuální počítač s Linuxem a odeslat jako vlastní metriky do Azure monitoru. 

 ![Přehled agenta měsíci](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Odeslat vlastní metriky 

Pro účely tohoto kurzu jsme nasadit virtuální počítač s Linuxem s operačním systémem Ubuntu 16.04 LTS. Telegraf agent se podporuje pro většinu operačních systémů Linux. Debian a ot. / min balíčky jsou k dispozici společně s nezabalené binární soubory Linuxu na portál pro stažení InfluxData. Najdete v článku tohoto průvodce instalací další Telegraf pokyny k instalaci a možnosti. 

Přihlaste se k [webu Azure Portal](https://portal.azure.com)

Pokud chcete vytvořit nový virtuální počítač s Linuxem: 

1. Klikněte na tlačítko **vytvořit prostředek** možnost v levém navigačním podokně. 
1. Vyhledejte *virtuálního počítače*  
1. Vyberte *Ubuntu 16.04 LTS* a klikněte na tlačítko **Create** 
1. Zadejte název virtuálního počítače jako *MyTelegrafVM*.  
1. Ponechat typ disku jako **SSD**, zadejte **uživatelské jméno**, jako například *azureuser*. 
1. Pro *typ ověřování*vyberte **heslo**, zadejte heslo, které použijete později k SSH do tohoto virtuálního počítače. 
1. Zvolit **vytvořit novou skupinu prostředků**, zadejte název, jako třeba *myResourceGroup*.  Zvolte požadované umístění a pak vyberte **OK**. 

     ![Vytvoření virtuálního počítače s Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Vyberte velikost virtuálního počítače. Můžete filtrovat podle výpočetní typ nebo typ disku, např. 

     ![Velikost virtuálního počítače telegrafní přehled agenta](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Na **stránka nastavení**v **sítě** > **skupinu zabezpečení sítě**   >  ** Vyberte veřejné příchozí porty**vyberte *HTTP* a *SSH (22)*. Ponechejte zbývající výchozí hodnoty a vyberte **OK**. 

1. Na stránce Souhrn vyberte možnost vytvořit a spusťte nasazování virtuálního počítače. 

1. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače. 

1. V okně virtuálního počítače, přejděte **Identity** kartu a ujistěte se váš virtuální počítač má identitu přiřazenou systémem *na*. 
 
![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači 

Vytvořte připojení SSH k virtuálnímu počítači. Klikněte na tlačítko Připojit na stránce Přehled pro váš virtuální počítač. 

![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

V okně připojení k stránce virtuálního počítače ponechte výchozí možnosti pro připojení s názvem DNS přes port 22. Při přihlášení pomocí virtuálního počítače se zobrazí místní účet příkazu připojení. Kliknutím na tlačítko příkaz zkopírujte. Následující příklad ukazuje, jak vypadá příkaz pro připojení přes SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Vložte připojení SSH do prostředí, jako jsou Azure Cloud Shell příkaz prostředí Bash na Ubuntu ve Windows, nebo pomocí klienta SSH podle vašeho výběru vytvořte připojení. 

## <a name="install-and-configure-telegraf"></a>Instalace a konfigurace Telegraf 

Chcete-li nainstalovat balíček Debian Telegraf do virtuálního počítače, spusťte následující příkazy z relace SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf na konfigurační soubor definuje Telegraf jeho operace. Ve výchozím nastavení, je nainstalována příklad konfiguračního souboru v cestě "/ etc/telegraf/telegraf.conf". Příklad konfiguračního souboru obsahuje seznam všech možných vstupní a výstupní moduly plug-in. Ale budeme vytvářet vlastní konfigurační soubor a agenta pomocí následujících příkazů 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> Výše uvedené povolí pouze dva vstupní moduly plug-in "cpu" a "paměť", můžete přidat více vstupních moduly plug-in (Docker, MySQL, NGINX atd.) v závislosti na úlohu spuštěnou na vašem počítači. Úplný seznam vstupních moduly plug-in najdete tady. 

Nakonec pomocí novou konfiguraci spuštění agenta, jsme vynutit agenta a spouštění spuštěním následujících příkazů 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agent teď bude shromažďovat metriky ze všech vstupních moduly plug-in zadaný a posílat do Azure monitoru. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Vykreslení Telegraf metrik na webu Azure Portal 

1. Otevřít [webu Azure portal](https://portal.azure.com) 

1. Přejít na nové kartě monitorování a pak vyberte **metriky**.  
     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Vyberte svůj virtuální počítač v modulu pro výběr prostředků

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Vyberte *Telegraf/procesoru* obor názvů a vyberte *usage_system* metriku. Můžete vybrat filtr podle dimenzí na tuto metriku nebo rozdělit na ně.  

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Další konfigurace 

Výše uvedené názorný postup obsahuje informace o tom, jak nakonfigurovat agenta Telegraf shromažďovat metriky z několika základní vstupní moduly plug-in. Telegraf agent obsahuje podporu pro víc než 150 vstupní moduly plug-in, se některé podpůrné další možnosti konfigurace. InfluxData publikoval [seznam podporovaných modulů plug-in](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) a pokyny, [způsob jejich konfigurace](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Kromě toho tento návod povolil, že pomocí kterých lze generovat metriky o virtuálním počítači je agent nasazen na Telegraf agenta. Telegraf agent také slouží jako kolekce a server pro předávání metrik za další prostředky. Další informace o konfiguraci agenta ke generování metriky pro ostatní prostředky Azure, najdete v článku [Azure Monitor vlastní metrika výstupu Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač, Delete a potom potvrďte název skupiny prostředků pro odstranění. 

## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).


