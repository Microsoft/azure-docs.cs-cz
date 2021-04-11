---
title: Jak nainstalovat a spustit kontejner prostorových analýz – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Kontejner prostorových analýz vám umožní detekovat lidi a vzdálenosti.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d257a77940b460bf8be64e3f8376353a859365f7
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284715"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Instalace a spuštění kontejneru prostorové analýzy (Preview)

Kontejner prostorových analýz vám umožňuje analyzovat streamování videa v reálném čase, abyste pochopili prostorové vztahy mezi lidmi, jejich pohybem a interakcemi s objekty ve fyzických prostředích. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> počítačové zpracování obrazu </a> pro úroveň Standard S1 v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * Ke spuštění kontejneru prostorové analýzy budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod budete používat později.


### <a name="spatial-analysis-container-requirements"></a>Požadavky na kontejner prostorových analýz

Ke spuštění kontejneru prostorových analýz budete potřebovat výpočetní zařízení s [grafickým procesorem NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Doporučujeme, abyste používali [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) s akcelerací GPU, ale kontejner běží na jakémkoli jiném počítači, který splňuje minimální požadavky. Do tohoto zařízení odkazujeme jako na hostitelském počítači.

#### <a name="azure-stack-edge-device"></a>[Azure Stack hraniční zařízení](#tab/azure-stack-edge)

Azure Stack Edge je řešení typu hardware jako služba a hraniční výpočetní zařízení s podporou AI s možnostmi přenosu dat v síti. Podrobné pokyny pro přípravu a nastavení najdete v [dokumentaci k Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Stolní počítač](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware

* 4 GB systémová paměť RAM
* 4 GB paměti RAM GPU
* PROCESOR s 8 jádry
* 1 grafický procesor NVIDIA Tesla T4
* 20 GB místa na disku

#### <a name="recommended-hardware"></a>Doporučený hardware

* paměť RAM 32 GB systému
* 16 GB paměti RAM GPU
* PROCESOR s 8 jádry
* 2 grafické procesory NVIDIA Tesla T4
* 50 GB prostoru SSD

V tomto článku budete stahovat a instalovat následující softwarové balíčky. Hostitelský počítač musí být schopný spustit následující (pokyny najdete níže):

* [Grafické ovladače NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) a [Sada nástrojů NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Konfigurace pro [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (multi-proces Service).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) a [NVIDIA – Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) runtime.

#### <a name="azure-vm-with-gpu"></a>[Virtuální počítač Azure s grafickým procesorem](#tab/virtual-machine)
V našem příkladu budeme používat [virtuální počítač řady NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který má jeden grafický procesor K80.

---

| Požadavek | Popis |
|--|--|
| Camera | Kontejner prostorových analýz není svázaný s konkrétní značkou kamery. Zařízení kamery potřebuje: podporuje kódování protokolu RTSP (Real-Time streaming Protocol) a H. 264, které je dostupné pro hostitelský počítač a umožňuje streamování při řešení 15FPS a 1080p. |
| Operační systém Linux | Na hostitelském počítači musí být nainstalovaný [Ubuntu Desktop 18,04 LTS](http://releases.ubuntu.com/18.04/) .  |


## <a name="request-approval-to-run-the-container"></a>Požádat o schválení ke spuštění kontejneru

Vyplňte a odešlete [formulář žádosti](https://aka.ms/csgate) , který vyžádá schválení pro spuštění kontejneru.

Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a pošle vám e-mail s rozhodnutím.

> [!IMPORTANT]
> * Ve formuláři je nutné použít e-mailovou adresu přidruženou k ID předplatného Azure.
> * Počítačové zpracování obrazu prostředek, který používáte ke spuštění kontejneru, se musí vytvořit s schváleným ID předplatného Azure.

Po schválení budete moct kontejner spustit po jeho stažení ze služby Microsoft Container Registry (MCR), která je popsána dále v článku.

Pokud vaše předplatné Azure neschválíte, nebudete moct kontejner spustit.

## <a name="set-up-the-host-computer"></a>Nastavení hostitelského počítače

Doporučuje se pro hostitelský počítač použít Azure Stack hraniční zařízení. Pokud konfigurujete jiné zařízení nebo **virtuální počítač** , pokud používáte virtuální počítač, klikněte na **desktopový počítač** .

#### <a name="azure-stack-edge-device"></a>[Azure Stack hraniční zařízení](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Konfigurace výpočtů na portálu Azure Stack Edge 
 
Prostorová analýza používá výpočetní funkce Azure Stackho okraje ke spuštění řešení AI. Pokud chcete povolit výpočetní funkce, ujistěte se, že: 

* Připojili jste se k zařízení Azure Stack Edge [a aktivovali](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) jste ho. 
* Máte klientský systém Windows se spuštěným prostředím PowerShell 5,0 nebo novějším, abyste měli přístup k zařízení.  
* Chcete-li nasadit cluster Kubernetes, je nutné nakonfigurovat Azure Stack hraniční zařízení prostřednictvím **místního uživatelského rozhraní** v [Azure Portal](https://portal.azure.com/): 
  1. Povolte funkci COMPUTE na zařízení Azure Stack Edge. Pokud chcete povolit výpočetní prostředky, ve webovém rozhraní pro vaše zařízení navštivte stránku **COMPUTE** . 
  2. Vyberte síťové rozhraní, které chcete povolit pro výpočetní prostředky, a pak klikněte na **Povolit**. Tím se na svém zařízení vytvoří virtuální přepínač na tomto síťovém rozhraní.
  3. Ponechte Kubernetes IP adresy uzlu testu a IP adresy externích služeb Kubernetes prázdné.
  4. Klikněte na **Použít**. Tato operace může trvat přibližně dvě minuty. 

![Konfigurace COMPUTE](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Nastavení role hraničních výpočtů a vytvoření prostředku IoT Hub

V [Azure Portal](https://portal.azure.com/)přejděte na prostředek Azure Stack Edge. Na stránce **Přehled** nebo v navigačním seznamu klikněte na tlačítko získat položku Edge COMPUTE – **Začínáme** . Na dlaždici  **Konfigurace hraničních výpočtů**   klikněte na **Konfigurovat**. 

![Odkaz](media/spatial-analysis/configure-edge-compute-tile.png)

Na stránce **Konfigurace výpočetních hraničních** zařízení   Vyberte existující IoT Hub nebo si vytvořte novou. Ve výchozím nastavení se k vytvoření IoT Hub prostředku používá cenová úroveň Standard (S1). Pokud chcete použít IoT Hub prostředek úrovně Free, vytvořte ho a pak ho vyberte. Prostředek IoT Hub používá stejné předplatné a skupinu prostředků, které používá prostředek Azure Stack Edge. 

Klikněte na **Vytvořit**. Vytváření prostředků IoT Hub může trvat několik minut. Po vytvoření prostředku IoT Hub se aktualizuje dlaždice **Konfigurace hraničních výpočtů** , aby se zobrazila nová konfigurace. Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte **Zobrazit** na dlaždici **Konfigurovat výpočty**   .

Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V zařízení IoT Edge již bude spuštěn modul runtime Azure IoT Edge.

> [!NOTE]
> * Pro IoT Edge zařízení je momentálně podporovaná jenom Platforma Linux. Pomoc při řešení potíží se zařízením Azure Stack Edge naleznete v článku [protokolování a řešení potíží](spatial-analysis-logging.md) .
> * Další informace o tom, jak nakonfigurovat zařízení IoT Edge pro komunikaci pomocí proxy server, najdete v tématu [konfigurace IoT Edge zařízení pro komunikaci prostřednictvím proxy server](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Povolit MPS na Azure Stack Edge 

1. Spusťte relaci Windows PowerShellu jako správce. 

2. Ujistěte se, že je ve vašem klientovi spuštěná služba Vzdálená správa systému Windows. V terminálu PowerShellu použijte následující příkaz. 
    
    ```powershell
    winrm quickconfig
    ```
    
    Pokud se zobrazí upozornění týkající se výjimky brány firewall, zkontrolujte typ připojení k síti a Prohlédněte si dokumentaci k [Vzdálená správa systému Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) .

3. Přiřaďte proměnnou k IP adrese zařízení. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Chcete-li přidat IP adresu vašeho zařízení do seznamu důvěryhodných hostitelů klienta, použijte následující příkaz: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Na zařízení spusťte relaci prostředí Windows PowerShell. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Po zobrazení výzvy zadejte heslo. Použijte stejné heslo, které se použije k přihlášení k místnímu webovému uživatelskému rozhraní. Výchozí heslo místního webového uživatelského rozhraní je `Password1` .

Zadejte `Start-HcsGpuMPS` , chcete-li na zařízení spustit službu MPS. 

Pomoc při řešení potíží se zařízením Azure Stack Edge najdete v tématu [Poradce při potížích se zařízením Azure Stack Edge](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) . 

#### <a name="desktop-machine"></a>[Stolní počítač](#tab/desktop-machine)

Pokud hostitelský počítač není Azure Stack hraniční zařízení, postupujte podle těchto pokynů.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Instalace grafických ovladačů NVIDIA CUDA Toolkit a NVIDIA na hostitelském počítači

K instalaci požadovaných grafických ovladačů NVIDIA a sady CUDA Toolkit použijte následující skript bash.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Restartujte počítač a spusťte následující příkaz.

```bash
nvidia-smi
```

Měli byste vidět následující výstup.

![Výstup ovladače NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Instalace Docker CE a NVIDIA-docker2 na hostitelském počítači

V hostitelském počítači nainstalujte Docker CE.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Nainstalujte balíček softwaru *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Povolit na hostitelském počítači NVIDIA MPS

> [!TIP]
> * Neinstalujte sady Management Pack, pokud je výpočetní funkce GPU menší než 7. x (pre Volta). Informace najdete v tématu [Kompatibilita CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) . 
> * Spusťte pokyny MPS z okna terminálu v hostitelském počítači. Není uvnitř vaší instance kontejneru Docker.

Pro dosažení optimálního výkonu a využití nakonfigurujte GPU (GPU) hostitelského počítače pro [víceprocesorové služby (MPS) NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Spusťte pokyny MPS z okna terminálu v hostitelském počítači.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Konfigurace Azure IoT Edge na hostitelském počítači

Pokud chcete nasadit kontejner prostorových analýz v hostitelském počítači, vytvořte instanci služby [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) pomocí cenové úrovně Standard (S1) nebo Free (F0). 

Pomocí rozhraní příkazového řádku Azure vytvořte instanci služby Azure IoT Hub. Nahraďte parametry tam, kde je to vhodné. Alternativně můžete vytvořit IoT Hub Azure na [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Dostupné oblasti najdete v tématu [Podpora oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Bude nutné nainstalovat [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 1.0.9 verze. Chcete-li stáhnout správnou verzi, postupujte podle následujících kroků:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Zkopírujte vygenerovaný seznam.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Nainstalujte veřejný klíč Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualizujte seznamy balíčků na vašem zařízení.

```bash
sudo apt-get update
```

Instalace verze 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

V dalším kroku Zaregistrujte hostitelský počítač jako zařízení IoT Edge ve vaší instanci IoT Hub pomocí [připojovacího řetězce](../../iot-edge/how-to-register-device.md).

Zařízení IoT Edge musíte připojit k Azure IoT Hub. Je nutné zkopírovat připojovací řetězec z IoT Edge zařízení, které jste vytvořili dříve. Případně můžete spustit níže uvedený příkaz v rozhraní příkazového řádku Azure CLI.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

V hostitelském počítači otevřeném  `/etc/iotedge/config.yaml` pro úpravy. Nahraďte `ADD DEVICE CONNECTION STRING HERE` připojovacím řetězcem. Uložte soubor a zavřete ho. Spuštěním tohoto příkazu restartujte službu IoT Edge v hostitelském počítači.

```bash
sudo systemctl restart iotedge
```

Nasaďte kontejner prostorových analýz jako modul IoT na hostitelském počítači, a to buď z [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) nebo prostřednictvím rozhraní příkazového [řádku Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Pokud používáte portál, nastavte identifikátor URI image na umístění vašeho Azure Container Registry. 

Pomocí následujících kroků nasaďte kontejner pomocí Azure CLI.

#### <a name="azure-vm-with-gpu"></a>[Virtuální počítač Azure s grafickým procesorem](#tab/virtual-machine)

Virtuální počítač Azure s grafickým procesorem (GPU) se dá použít taky ke spouštění prostorových analýz. Níže uvedený příklad použije virtuální počítač [řady NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který má jeden grafický procesor K80.

#### <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Otevřete Průvodce [vytvořením virtuálního počítače](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) v Azure Portal.

Zadejte název virtuálního počítače a vyberte oblast, která se má (US) Západní USA 2. Nezapomeňte nastavit `Availability Options` na "bez nutnosti redundance infrastruktury". Úplnou konfiguraci a další krok pro vyhledání správné velikosti virtuálního počítače najdete na obrázku níže. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Podrobnosti o konfiguraci virtuálního počítače." lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Velikost virtuálního počítače vyhledáte tak, že vyberete "Zobrazit všechny velikosti" a pak zobrazíte seznam pro velikosti virtuálních počítačů, které nejsou Premium Storage, zobrazené níže.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Velikosti virtuálních počítačů." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Pak vyberte buď **NC6** , nebo **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="propagační výběr" lightbox="media/spatial-analysis/promotional-selection.png":::

Pak vytvořte virtuální počítač. Po vytvoření přejděte k prostředku virtuálního počítače v Azure Portal a vyberte `Extensions` v levém podokně. Zobrazí se okno rozšíření se všemi dostupnými rozšířeními. Vyberte `NVIDIA GPU Driver Extension` , klikněte na vytvořit a dokončete průvodce.

Po úspěšném použití rozšíření přejděte na hlavní stránku virtuálního počítače v Azure Portal a klikněte na `Connect` . K virtuálnímu počítači se dá přistup přes SSH nebo RDP. Protokol RDP bude užitečný, protože bude umožňovat zobrazení okna Vizualizér (vysvětlení později). Pomocí následujících [kroků](../../virtual-machines/linux/use-remote-desktop.md) NAKONFIGURUJTE přístup RDP a otevřete připojení ke vzdálené ploše virtuálního počítače.

### <a name="verify-graphics-drivers-are-installed"></a>Ověření instalace grafických ovladačů

Spusťte následující příkaz a ověřte, zda byly ovladače grafiky úspěšně nainstalovány. 

```bash
nvidia-smi
```

Měli byste vidět následující výstup.

![Výstup ovladače NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Instalace Docker CE a NVIDIA-docker2 na virtuálním počítači

Spusťte následující příkazy po jednom, abyste mohli na virtuální počítač nainstalovat Docker CE a NVIDIA-docker2.

V hostitelském počítači nainstalujte Docker CE.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Nainstalujte balíček softwaru *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Teď, když jste nastavili a nakonfigurovali svůj virtuální počítač, nakonfigurujte Azure IoT Edge podle následujících kroků. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Konfigurace Azure IoT Edge na virtuálním počítači

Pokud chcete nasadit kontejner prostorových analýz na virtuálním počítači, vytvořte instanci služby [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) s využitím cenové úrovně Standard (S1) nebo Free (F0).

Pomocí rozhraní příkazového řádku Azure vytvořte instanci služby Azure IoT Hub. Nahraďte parametry tam, kde je to vhodné. Alternativně můžete vytvořit IoT Hub Azure na [Azure Portal](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Dostupné oblasti najdete v tématu [Podpora oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Bude nutné nainstalovat [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 1.0.9 verze. Chcete-li stáhnout správnou verzi, postupujte podle následujících kroků:

Ubuntu Server 18,04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Zkopírujte vygenerovaný seznam.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Nainstalujte veřejný klíč Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualizujte seznamy balíčků na vašem zařízení.

```bash
sudo apt-get update
```

Instalace verze 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

V dalším kroku zaregistrujete virtuální počítač jako zařízení IoT Edge ve vaší instanci IoT Hub pomocí [připojovacího řetězce](../../iot-edge/how-to-register-device.md).

Zařízení IoT Edge musíte připojit k Azure IoT Hub. Je nutné zkopírovat připojovací řetězec z IoT Edge zařízení, které jste vytvořili dříve. Případně můžete spustit níže uvedený příkaz v rozhraní příkazového řádku Azure CLI.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Na virtuálním počítači otevřeném  `/etc/iotedge/config.yaml` pro úpravy. Nahraďte `ADD DEVICE CONNECTION STRING HERE` připojovacím řetězcem. Uložte soubor a zavřete ho. Spuštěním tohoto příkazu restartujte službu IoT Edge na virtuálním počítači.

```bash
sudo systemctl restart iotedge
```

Nasaďte kontejner prostorových analýz jako modul IoT na virtuálním počítači, a to buď z [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) nebo prostřednictvím rozhraní příkazového [řádku Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Pokud používáte portál, nastavte identifikátor URI image na umístění vašeho Azure Container Registry. 

Pomocí následujících kroků nasaďte kontejner pomocí Azure CLI.

---

### <a name="iot-deployment-manifest"></a>Manifest nasazení IoT

Chcete-li zjednodušit nasazování kontejnerů na více hostitelských počítačích, můžete vytvořit soubor manifestu nasazení a zadat možnosti vytvoření kontejneru a proměnné prostředí. Příklad manifestu nasazení [pro Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [dalších stolních počítačích](https://go.microsoft.com/fwlink/?linkid=2152270)a [virtuálního počítače Azure s grafickým procesorem](https://go.microsoft.com/fwlink/?linkid=2152189) na GitHubu najdete zde.

V následující tabulce jsou uvedeny různé proměnné prostředí používané modulem IoT Edge. Můžete je také nastavit v manifestu nasazení výše, pomocí `env` atributu v `spatialanalysis` :

| Název nastavení | Hodnota | Popis|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Příjemce Podrobné | Úroveň protokolování, vyberte jednu ze dvou hodnot.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Neupravovat|
| ARCHON_PERF_MARKER| false (nepravda)| Nastavte tuto hodnotu na true pro protokolování výkonu, v opačném případě by měla být false.| 
| ARCHON_NODES_LOG_LEVEL | Příjemce Podrobné | Úroveň protokolování, vyberte jednu ze dvou hodnot.|
| OMP_WAIT_POLICY | PASIVNÍ | Neupravovat|
| QT_X11_NO_MITSHM | 1 | Neupravovat|
| APIKEY | váš klíč rozhraní API| Tuto hodnotu z prostředku Počítačové zpracování obrazu Shromážděte z Azure Portal. Můžete ji najít v části **klíč a koncový bod** pro váš prostředek. |
| FAKTURACE | identifikátor URI koncového bodu| Tuto hodnotu z prostředku Počítačové zpracování obrazu Shromážděte z Azure Portal. Můžete ji najít v části **klíč a koncový bod** pro váš prostředek.|
| KONKRÉTNÍ | vyjádřit | Tato hodnota musí být nastavena na hodnotu *přijmout* , aby bylo možné kontejner spustit. |
| Otevřete | : 1 | Tato hodnota musí být stejná jako výstup `echo $DISPLAY` na hostitelském počítači. Hraniční zařízení Azure Stack neobsahují displej. Toto nastavení se nedá použít.|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Tuto proměnnou prostředí přidejte, pokud vaše GPU **nemá** T4 nebo NVIDIA 2080 ti.|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Tuto proměnnou prostředí přidejte, pokud vaše GPU **nemá** T4 nebo NVIDIA 2080 ti.|
| KEY_ENV | Šifrovací klíč pomocného mechanismu | Přidejte tuto proměnnou prostředí, pokud Video_URL je nejasný řetězec. |
| IV_ENV | Inicializační vektor | Přidejte tuto proměnnou prostředí, pokud Video_URL je nejasný řetězec.|

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).

Když aktualizujete manifest nasazení pro [Azure Stack hraniční zařízení](https://go.microsoft.com/fwlink/?linkid=2142179), [stolní počítač](https://go.microsoft.com/fwlink/?linkid=2152270) nebo [virtuální počítač Azure s grafickým procesorem](https://go.microsoft.com/fwlink/?linkid=2152189) s vlastním nastavením a výběrem operací, můžete k nasazení kontejneru v hostitelském počítači, jako je IoT Edge modul, použít následující příkaz [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) .

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parametr  |Popis  |
|---------|---------|
| `--hub-name` | Název Azure IoT Hub. |
| `--content` | Název souboru nasazení. |
| `--target-condition` | Název zařízení IoT Edge pro hostitelský počítač. |
| `-–subscription` | ID nebo název předplatného |

Tento příkaz spustí nasazení. Přejděte na stránku vaší instance Azure IoT Hub v Azure Portal a zobrazte stav nasazení. Stav může být zobrazený jako *417 – konfigurace nasazení zařízení není nastavená* , dokud zařízení nedokončí stahování imagí kontejneru a začne běžet.

## <a name="validate-that-the-deployment-is-successful"></a>Ověření úspěšného nasazení

Existuje několik způsobů, jak ověřit, zda je kontejner spuštěn. Vyhledejte *běhový stav* v **nastavení modulu IoT Edge** pro modul prostorové analýzy v instanci Azure IoT Hub na Azure Portal. Ověřte, zda je *spuštěna* **požadovaná hodnota** a **hlášené hodnoty** pro *běhový stav* .

![Příklad ověření nasazení](./media/spatial-analysis/deployment-verification.png)

Po dokončení nasazení a spuštění kontejneru se v **hostitelském počítači** začnou odesílat události do Azure IoT Hub. Pokud jste použili `.debug` verzi operací, zobrazí se okno Vizualizátor pro každý fotoaparát, který jste nakonfigurovali v manifestu nasazení. Nyní můžete definovat řádky a zóny, které chcete monitorovat v manifestu nasazení, a podle pokynů znovu nasadit. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Konfigurace operací prováděných prostorovou analýzou

K nakonfigurování kontejneru pro použití připojených fotoaparátů, konfiguraci operací a dalších operací budete muset použít [operace prostorové analýzy](spatial-analysis-operations.md) . Pro každé zařízení kamery, které nakonfigurujete, se operace pro prostorovou analýzu vygenerují výstupní proud zpráv JSON, které se odešlou do vaší instance Azure IoT Hub.

## <a name="use-the-output-generated-by-the-container"></a>Použít výstup generovaný kontejnerem

Pokud chcete začít spotřebovávat výstup generovaný kontejnerem, přečtěte si následující články:

*    K připojení ke koncovému bodu Azure IoT Hub a příjmu událostí použijte sadu Azure Event hub SDK pro zvolený programovací jazyk. Další informace najdete v tématu [čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
*    Nastavte směrování zpráv v Azure IoT Hub pro odesílání událostí do jiných koncových bodů nebo ukládání událostí do Azure Blob Storage atd. Další informace najdete v tématu [IoT Hub směrování zpráv](../../iot-hub/iot-hub-devguide-messages-d2c.md) . 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Spuštění prostorové analýzy s nahraným videosouborem

Můžete použít prostorovou analýzu s nahraným nebo živým videem. Pokud chcete použít prostorovou analýzu pro zaznamenané video, zkuste nahrávat videosoubor a uložit ho jako soubor MP4. Vytvořte účet Blob Storage v Azure nebo použijte existující. Pak v Azure Portal aktualizujte následující nastavení úložiště objektů BLOB:
    1. Změnit **zabezpečený přenos vyžadovaný** jako **zakázaný**
    2. Změnit **povolený** **veřejný přístup k objektu BLOB**

Přejděte do části **Container** a buď vytvořte nový kontejner, nebo použijte existující. Pak odešlete videosoubor do kontejneru. Rozbalte nastavení souboru pro nahraný soubor a vyberte **Generovat SAS**. Nezapomeňte nastavit **Datum vypršení platnosti** dostatečně dlouho na pokrytí období testování. Nastavte **Povolené protokoly** na *http* (*https* není podporované).

Klikněte na **vygenerovat token SAS a adresu URL** a zkopírujte adresu URL SAS objektu BLOB. Nahraďte začínající `https` `http` a otestujte adresu URL v prohlížeči, který podporuje přehrávání videa.

Nahraďte `VIDEO_URL` v manifestu nasazení pro vaše [Azure Stack hraniční zařízení](https://go.microsoft.com/fwlink/?linkid=2142179), [stolní počítač](https://go.microsoft.com/fwlink/?linkid=2152270)nebo [virtuální počítač Azure pomocí GPU](https://go.microsoft.com/fwlink/?linkid=2152189) s adresou URL, kterou jste vytvořili, pro všechny grafy. Nastavte `VIDEO_IS_LIVE` na `false` a znovu nasaďte kontejner prostorové analýzy s aktualizovaným manifestem. Viz následující příklad.

Modul pro prostorové analýzy začne spotřebovávat videosoubor a bude se nepřetržitě automaticky přehrávat také.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problémy při spuštění nebo spuštění kontejneru, přečtěte si téma [telemetrie a řešení potíží](spatial-analysis-logging.md) , kde najdete kroky pro běžné problémy. Tento článek obsahuje také informace o generování a shromažďování protokolů a shromažďování stavu systému.

## <a name="billing"></a>Fakturace

Kontejner prostorové analýzy odesílá informace o fakturaci do Azure pomocí Počítačové zpracování obrazu prostředku na vašem účtu Azure. Použití prostorové analýzy ve verzi Public Preview je aktuálně zdarma. 

U kontejnerů Azure Cognitive Services není licencováno běžet bez připojení ke koncovému bodu měření/fakturace. Kontejnerům musíte povolit, aby informace o fakturaci komunikovaly s koncovým bodem fakturace. Kontejnery Cognitive Services neodesílají zákaznická data, jako je video nebo obrázek, který se analyzuje, do Microsoftu.


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro stažení, instalaci a spuštění kontejneru prostorové analýzy. Souhrn:

* Prostorová analýza je kontejner pro Linux pro Docker.
* Image kontejneru se stáhnou z Container Registry Microsoftu.
* Image kontejneru se spouštějí jako moduly IoT v Azure IoT Edge.
* Postup konfigurace kontejneru a jeho nasazení na hostitelském počítači.

## <a name="next-steps"></a>Další kroky

* [Nasazení webové aplikace počítající lidi](spatial-analysis-web-app.md)
* [Konfigurace operací prostorových analýz](spatial-analysis-operations.md)
* [Protokolování a řešení potíží](spatial-analysis-logging.md)
* [Průvodce umístěním kamery](spatial-analysis-camera-placement.md)
* [Průvodce umístěním zóny a linky](spatial-analysis-zone-line-placement.md)
