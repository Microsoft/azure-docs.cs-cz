---
title: Spusťte Azure IoT Edge na Windows Server Virtual Machines | Dokumentace Microsoftu
description: Azure IoT Edge pokyny k instalaci na Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159777"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Spusťte Azure IoT Edge na Windows Server Virtual Machines
Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu.

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky ke spuštění modulu runtime Azure IoT Edge na využití virtuálních počítačů Windows serveru 2019 [systému Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) nabídky Azure Marketplace. Postupujte podle pokynů na adrese [nainstalovat modul runtime Azure IoT Edge](how-to-install-iot-edge-windows.md) ve Windows pro použití s jinými verzemi.

> [!NOTE]
> Modul runtime IoT Edge ve Windows serveru je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Nasazení na webu Azure Marketplace
1.  Přejděte [systému Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) nabídky Azure Marketplace nebo hledáním "Windows serveru" v [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Vyberte **získat teď** 
3.  V **plán softwaru**, vyhledejte "2019 Datacenter Server Core s kontejnery pro Windows Server" a pak vyberte **pokračovat** v dalším dialogovém okně.
    * Tyto pokyny můžete použít také u jiných verzí Windows serveru s kontejnery
4.  Jednou na webu Azure Portal, vyberte **vytvořit** a postupujte podle pokynů průvodce k nasazení virtuálního počítače. 
    *   Pokud je poprvé vyzkoušet si virtuální počítač, je nejjednodušší použití hesla a povolení protokolu RDP a SSH v nabídce veřejného portu pro příchozí spojení. 
    *   Pokud nemáte úlohy náročné na prostředek, měli byste upgradovat velikost virtuálního počítače tak, že přidáte víc procesorů nebo paměti.
5.  Po nasazení virtuálního počítače, nakonfigurujte ji k připojení ke službě IoT Hub:
    1.  Zkopírujte připojovací řetězec zařízení ze zařízení IoT Edge vytvořené ve službě IoT Hub (můžete použít [zaregistrovat nová zařízení Azure IoT Edge z portálu Azure portal](how-to-register-device-portal.md) příručce s postupy, pokud nejste obeznámeni s tímto procesem)
    1.  Vyberte prostředek nově vytvořenému virtuálnímu počítači z webu Azure portal a otevřete **spusťte příkaz** možnost
    1.  Vyberte **RunPowerShellScript** možnost
    1.  Tento skript zkopírujte do příkazového okna vaším připojovacím řetězcem zařízení: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Spusťte skript pro instalaci modulu runtime Edge a nastavte připojovací řetězec tak, že vyberete **spuštění**
    1.  Po jedné až dvou minutách měli byste vidět zprávu, že byl nainstalován a úspěšně zřízena modulu runtime Edge.


## <a name="deploy-from-the-azure-portal"></a>Nasazení na webu Azure Portal
1. Na webu Azure Portal, vyhledejte "Windows Server" a vyberte **systému Windows Server. 2019 Datacenter** zahájíte pracovní postup vytvoření virtuálního počítače. 
2. Z **vyberte plán softwaru** zvolte "2019 Datacenter Server Core s kontejnery pro Windows Server" a pak vyberte **Create**
3. Úplné pokyny na "nasazení z webu Azure Marketplace" kroku 5 výše.

## <a name="deploy-from-azure-cli"></a>Nasazení z příkazového řádku Azure
1. Pokud používáte rozhraní příkazového řádku Azure na pracovní ploše, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```
    
1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Seznam vašich předplatných:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopírování z pole ID předplatného pro předplatné, které chcete použít
   1. S ID, které jste zkopírovali, spusťte tento příkaz:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Vytvořte novou skupinu prostředků (nebo zadejte existující v dalších krocích):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Vytvoření nového virtuálního počítače:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Tento příkaz vás vyzve k zadání hesla, ale můžete přidat možnost `--admin-password` snadněji nastavit ve skriptu
   * Bitová kopie systému Windows Server Core má příkazový řádek podporu pouze pomocí vzdálené plochy, takže pokud byste o ni úplné desktopové prostředí, zadejte `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jako obrázek

1. Nastavit připojovací řetězec zařízení (provedením [pomocí Azure CLI zaregistrujte nové zařízení Azure IoT Edge](how-to-register-device-cli.md) příručce s postupy, pokud nejste obeznámeni s tímto procesem):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s modulu runtime Edge instalaci správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

Aktualizace stávající instalace na nejnovější verzi služby IoT Edge, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Další informace o používání Windows virtuální počítače [dokumentace k Windows Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

Pokud chcete se SSH do tohoto virtuálního počítače po instalaci, postupujte [instalace systému OpenSSH pro Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) průvodce pomocí vzdálené plochy nebo vzdálené prostředí powershell.
