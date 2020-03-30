---
title: Spuštění Azure IoT Edge na virtuálních počítačích se systémem Windows Server | Dokumenty společnosti Microsoft
description: Pokyny k nastavení Azure IoT Edge na virtuálních počítačích Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045911"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Spuštění Azure IoT Edge na virtuálních počítačích s Windows Serverem

Azure IoT Edge runtime je to, co změní zařízení na zařízení IoT Edge. Runtime lze nasadit na zařízení tak malá jako Raspberry Pi nebo velká jako průmyslový server. Jakmile je zařízení nakonfigurované s runtime IoT Edge, můžete do něj začít nasazovat obchodní logiku z cloudu.

Další informace o tom, jak funguje runtime IoT Edge a jaké součásti jsou zahrnuty, najdete [v tématu Principy runtime Azure IoT Edge a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedeny kroky ke spuštění runtime Azure IoT Edge na virtuálním počítači s Windows Serverem 2019 pomocí nabídky [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Postupujte podle pokynů na [webu Instalace runtime Azure IoT Edge](how-to-install-iot-edge-windows.md) v systému Windows pro použití s jinými verzemi.

## <a name="deploy-from-the-azure-marketplace"></a>Nasazení z Azure Marketplace

1. Přejděte na nabídku [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace nebo vyhledáním "Windows Serveru" na [Azure Marketplace.](https://azuremarketplace.microsoft.com/)
2. Vyberte **ZÍSKAT NYNÍ**
3. V **plánu softwaru**najděte "Windows Server 2019 Datacenter Server Core with Containers" a pak v dalším dialogovém okně vyberte **Pokračovat.**
    * Tyto pokyny můžete použít také pro jiné verze systému Windows Server s kontejnery.
4. Na webu Azure Portal vyberte **Vytvořit** a postupujte podle průvodce k nasazení virtuálního počítače.
    * Pokud je to poprvé, vyzkoušet virtuální počítač, je nejjednodušší použít heslo a povolit RDP a SSH ve veřejné nabídce příchozí port.
    * Pokud máte úlohu náročnou na prostředky, měli byste upgradovat velikost virtuálního počítače přidáním dalších procesorů nebo paměti.
5. Po nasazení virtuálního počítače ho nakonfigurujte tak, aby se připojil k vašemu centru IoT Hub:
    1. Zkopírujte připojovací řetězec zařízení ze zařízení IoT Edge vytvořeného ve vašem IoT Hubu. Podívejte se na postup [Načíst připojovací řetězec na webu Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Vyberte nově vytvořený prostředek virtuálního počítače z portálu Azure a otevřete možnost **spustit příkaz**
    1. Výběr volby **RunPowerShellScript**
    1. Zkopírujte tento skript do příkazového okna pomocí připojovacího řetězce zařízení:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Spusťte skript pro instalaci runtime IoT Edge a nastavte připojovací řetězec výběrem **možnosti Spustit**
    1. Po minutě nebo dvou, měli byste se zobrazit zpráva, že edge runtime byla nainstalována a zřídit úspěšně.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z webu Azure Portal

1. Na webu Azure Portal vyhledejte "Windows Server" a vyberte **Windows Server 2019 Datacenter** a zahajte pracovní postup vytváření virtuálních aplikací.
2. V **možnosti Vybrat softwarový plán** zvolte "Jádro serveru datového centra Windows Server 2019 s kontejnery" a pak vyberte **Vytvořit**
3. Dokončete krok 5 v pokynech "Nasazení z Azure Marketplace" výše.

## <a name="deploy-from-azure-cli"></a>Nasazení z Azure CLI

1. Pokud používáte Azure CLI na ploše, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```

1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Zobrazte výpis vašich předplatných:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Zkopírujte pole SubscriptionID pro předplatné, které chcete použít.
   1. Spusťte tento příkaz s id, které jste zkopírovali:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Vytvořte novou skupinu prostředků (nebo v dalších krocích zadejte existující skupinu):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Vytvoření nového virtuálního počítače:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Tento příkaz vás vyzve k zadání hesla, `--admin-password` ale můžete přidat možnost jeho snadněji nastavit ve skriptu
   * Bitová kopie Windows Server Core podporuje příkazový řádek pouze se vzdálenou plochou, takže pokud chcete mít plnou plochu, zadejte `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jako obrázek

1. Nastavte připojovací řetězec zařízení (Můžete postupovat podle [načíst připojovací řetězec pomocí postupu Azure CLI,](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) pokud nejste obeznámeni s tímto procesem):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s nainstalovaným modulem runtime, můžete [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy se správnou instalací runtime edge, podívejte se na stránku [pro řešení potíží.](troubleshoot.md)

Informace o aktualizaci existující instalace na nejnovější verzi technologie IoT Edge naleznete v [tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).

Další informace o používání virtuálních počítačů s Windows naleznete v dokumentaci k [virtuálním počítačům Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Pokud chcete SSH do tohoto virtuálního počítače po instalaci, postupujte podle [instalace OpenSSH pro Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) průvodce pomocí vzdálené plochy nebo vzdálené powershellu.
