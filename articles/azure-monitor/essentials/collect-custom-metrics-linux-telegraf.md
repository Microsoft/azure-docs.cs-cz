---
title: Shromažďování vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf
description: Pokyny, jak nasadit agenta InfluxData telegraf na virtuálním počítači Linux v Azure a nakonfigurovat agenta tak, aby publikoval metriky pro Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 204124240c6831ebb2c1df436736f475c48d98a8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048931"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Shromažďování vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf

Pomocí Azure Monitor můžete shromažďovat vlastní metriky prostřednictvím telemetrie aplikací, agenta spuštěného na vašich prostředcích Azure nebo dokonce i mimo jiné monitorovací systémy. Pak je můžete odeslat přímo do Azure Monitor. Tento článek poskytuje pokyny, jak nasadit agenta [InfluxData](https://www.influxdata.com/) telegraf na virtuálním počítači Linux v Azure a nakonfigurovat agenta tak, aby publikoval metriky pro Azure monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData telegraf Agent 

[Telegraf](https://docs.influxdata.com/telegraf/) je agentem řízený modul plug-in, který umožňuje shromažďování metrik z více než 150 různých zdrojů. V závislosti na tom, jaké úlohy na VIRTUÁLNÍm počítači běží, můžete nakonfigurovat agenta tak, aby při shromažďování metrik využili specializované vstupní moduly plug-in. Příklady jsou MySQL, NGINX a Apache. Pomocí modulů plug-in pro výstup pak může agent zapisovat do zvolených cílů. Agent telegraf se integruje přímo s Azure Monitor vlastní metriky REST API. Podporuje modul plug-in Azure Monitorho výstupu. Pomocí tohoto modulu plug-in může agent shromažďovat metriky specifické pro úlohy na VIRTUÁLNÍm počítači Linux a odesílat je jako vlastní metriky Azure Monitor. 

 ![Přehled telegrafního agenta](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Vlastní metriky se ve všech oblastech nepodporují. [Tady](./metrics-custom-overview.md#supported-regions) jsou uvedené podporované oblasti.

## <a name="send-custom-metrics"></a>Odeslat vlastní metriky 

V tomto kurzu nasadíme virtuální počítač Linux, na kterém běží operační systém Ubuntu 16,04 LTS. Agent telegraf se podporuje pro většinu operačních systémů Linux. Balíčky Debian i ot./min. jsou k dispozici společně s nebalenými binárními soubory Linux na [portálu pro stažení InfluxData](https://portal.influxdata.com/downloads). Další pokyny k instalaci a možnosti najdete v této [příručce k instalaci telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) . 

Přihlaste se na [Azure Portal](https://portal.azure.com).

> [!NOTE]  
> Pokud chcete migrovat pravidla pro klasické výstrahy a použít stávající virtuální počítač se systémem Linux, ujistěte se, že má počítač s virtuální nastavenou identitu přiřazenou **systémem.**

Vytvořte nový virtuální počítač pro Linux: 

1. V levém navigačním podokně vyberte možnost **vytvořit prostředek** . 
1. Vyhledejte **virtuální počítač**.  
1. Vyberte **Ubuntu 16,04 LTS** a vyberte **vytvořit**. 
1. Zadejte název virtuálního počítače, třeba **MyTelegrafVM**.  
1. Ponechte typ disku jako **SSD**. Pak zadejte **uživatelské jméno**, například **azureuser**. 
1. Jako **typ ověřování** vyberte **heslo**. Pak zadejte heslo, které budete později používat pro SSH do tohoto virtuálního počítače. 
1. Vyberte možnost **vytvořit novou skupinu prostředků**. Pak zadejte název, například **myResourceGroup**. Vyberte své **umístění**. Pak vyberte **OK**. 

    ![Vytvoření virtuálního počítače s Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Vyberte velikost virtuálního počítače. Můžete filtrovat například podle **Typu výpočtu** nebo **Typu disku**. 

    ![Přehled pro velikost vytelegrafního agenta pro virtuální počítače](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na stránce **Nastavení** **ve**  >  **skupině zabezpečení síťové sítě**  >  **Vyberte veřejné příchozí porty**, vyberte **http** a **SSH (22)**. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**. 

1. Na stránce Souhrn výběrem možnosti **Vytvořit** spusťte nasazení virtuálního počítače. 

1. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře okno Souhrn virtuálního počítače. 

1. V podokně virtuální počítač přejděte na kartu **Identita** . Ujistěte se, že váš virtuální počítač má identitu přiřazenou systémem, která je nastavená na **zapnuto**. 
 
    ![Identita virtuálního počítače telegraf ve verzi Preview](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači 

Vytvořte připojení SSH k virtuálnímu počítači. Na stránce Přehled pro váš virtuální počítač vyberte tlačítko **Připojit**. 

![Stránka s přehledem virtuálního počítače s telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na stránce **Připojení k virtuálnímu počítači** ponechte výchozí výběr možností pro připojení podle názvu DNS přes port 22. V rámci **přihlášení pomocí místního účtu virtuálního počítače** se zobrazí příkaz pro připojení. Vyberte tlačítko pro zkopírování příkazu. Následující příklad ukazuje, jak vypadá příkaz pro připojení přes SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Vložte příkaz pro připojení SSH do prostředí, jako je například Azure Cloud Shell nebo bash na Ubuntu ve Windows, nebo použijte pro vytvoření připojení klienta SSH podle vašeho výběru. 

## <a name="install-and-configure-telegraf"></a>Instalace a konfigurace telegraf 

K instalaci balíčku telegraf Debian na virtuální počítač spusťte následující příkazy z relace SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Konfigurační soubor telegraf definuje operace telegraf. Ve výchozím nastavení se v cestě **/etc/telegraf/telegraf.conf** nainstaluje Ukázkový konfigurační soubor. Ukázkový konfigurační soubor uvádí všechny možné vstupní a výstupní moduly plug-in. Vytvoříme ale vlastní konfigurační soubor a dá ho použít agent, a to spuštěním následujících příkazů: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Předchozí kód povoluje pouze dva vstupní moduly plug-in: **CPU** a **mem**. V závislosti na zatížení, které běží na vašem počítači, můžete přidat další vstupní moduly plug-in. Příklady jsou Docker, MySQL a NGINX. Úplný seznam vstupních modulů plug-in najdete v části **Další konfigurace** . 

Nakonec, pokud chcete, aby agent začal používat novou konfiguraci, vynutíme zastavení a spuštění agenta spuštěním následujících příkazů: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agent nyní bude shromažďovat metriky ze všech zadaných vstupních modulů plug-in a emituje je Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Vykreslení metriky telegraf v Azure Portal 

1. Otevřete [Azure Portal](https://portal.azure.com). 

1. Přejděte na kartu nové **monitorování** . Pak vyberte **metriky**.  

     ![Monitor – metriky (Preview)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. V selektoru prostředků vyberte svůj virtuální počítač.

     ![Graf metriky](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Vyberte obor názvů **telegraf/CPU** a vyberte metriku **usage_system** . Můžete zvolit filtrování podle dimenzí v této metrice nebo jejich rozdělení.  

     ![Vybrat obor názvů a metriku](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Další konfigurace 

Předchozí návod poskytuje informace o tom, jak nakonfigurovat agenta telegraf na shromažďování metrik z několika základních modulů plug-in. Agent telegraf podporuje více než 150 vstupních modulů plug-in, přičemž některé podporují další možnosti konfigurace. InfluxData publikoval [seznam podporovaných modulů plug-in](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) a pokyny, [jak je nakonfigurovat](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

V tomto návodu jste navíc použili agenta telegraf k vygenerování metrik o virtuálním počítači, na kterém je agent nasazený. Agenta telegraf lze také použít jako kolektor a předávané metriky pro další prostředky. Informace o tom, jak nakonfigurovat agenta tak, aby vygeneroval metriky pro další prostředky Azure, najdete v tématu [Azure monitor vlastní výstup metriky pro telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro virtuální počítač a vyberete **Odstranit**. Pak potvrďte název skupiny prostředků, která se má odstranit. 

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](./metrics-custom-overview.md).