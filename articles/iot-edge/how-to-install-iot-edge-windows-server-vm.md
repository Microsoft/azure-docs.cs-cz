---
title: Spuštění Azure IoT Edge na Windows serveru Virtual Machines | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na webu Marketplace pro Windows Server Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494056"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Spuštění Azure IoT Edge v systému Windows Server Virtual Machines

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku.

Další informace o tom, jak modul runtime IoT Edge funguje a jaké součásti jsou zahrnuté, najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky, jak spustit modul runtime Azure IoT Edge na virtuálním počítači s Windows serverem 2019 pomocí nabídky [Windows server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace. Postupujte podle pokynů v tématu [Instalace modulu runtime Azure IoT Edge](how-to-install-iot-edge-windows.md) ve Windows pro použití s jinými verzemi.

## <a name="deploy-from-the-azure-marketplace"></a>Nasazení z Azure Marketplace

1. Přejděte na nabídku [Windows server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace nebo vyhledejte v části Windows server na [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Vyberte **získat hned**
3. V části **plán softwaru**Najděte "Windows Server 2019 Datacenter Server Core with Containers" a potom v dalším dialogovém okně vyberte **pokračovat** .
    * Tyto pokyny můžete použít také pro jiné verze Windows serveru s kontejnery.
4. V Azure Portal vyberte **vytvořit** a postupujte podle pokynů průvodce a NASAĎTE virtuální počítač.
    * Pokud si virtuální počítač vyzkoušíte poprvé, je jednodušší použít heslo a povolit RDP a SSH v nabídce veřejného příchozího portu.
    * Pokud máte úlohu náročné na prostředky, měli byste upgradovat velikost virtuálního počítače přidáním dalších procesorů nebo paměti.
5. Po nasazení virtuálního počítače ho nakonfigurujte tak, aby se připojil k vašemu IoT Hub:
    1. Z IoT Edge zařízení vytvořeného v IoT Hub zkopírujte připojovací řetězec zařízení. Viz postup [Načtení připojovacího řetězce v Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Vyberte nově vytvořený prostředek virtuálního počítače z Azure Portal a otevřete možnost **Spustit příkaz** .
    1. Vyberte možnost **RunPowerShellScript**
    1. Zkopírujte tento skript do příkazového okna s připojovacím řetězcem zařízení:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Spusťte skript pro instalaci IoT Edge runtime a nastavte připojovací řetězec výběrem možnosti **Spustit** .
    1. Po minutě nebo dvou minutách by se měla zobrazit zpráva, že modul runtime Edge byl nainstalován a úspěšně zřízen.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z Azure Portal

1. V Azure Portal vyhledejte "Windows Server" a vyberte **Windows server 2019 Datacenter** , abyste mohli začít pracovní postup vytváření virtuálního počítače.
2. V části **Vyberte plán softwaru** zvolte Windows Server 2019 Datacenter Server Core with Containers a pak vyberte **vytvořit** .
3. Proveďte krok 5 v části nasazení z Azure Marketplace výše.

## <a name="deploy-from-azure-cli"></a>Nasazení z Azure CLI

1. Pokud na ploše používáte rozhraní příkazového řádku Azure CLI, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```

1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Zobrazte výpis vašich předplatných:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Zkopírujte pole SubscriptionID pro předplatné, které chcete použít.
   1. Spusťte tento příkaz s ID, které jste zkopírovali:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Vytvořte novou skupinu prostředků (nebo v dalších krocích určete existující):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Vytvořit nový virtuální počítač:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Tento příkaz vás vyzve k zadání hesla, ale můžete přidat možnost `--admin-password` pro jednodušší nastavení skriptu.
   * Základní image Windows serveru obsahuje podporu příkazového řádku jenom se vzdálenou plochou, takže pokud chcete, aby se jako image zobrazovala úplná plocha, zadejte `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jako obrázek.

1. Nastavení připojovacího řetězce zařízení (Pokud nejste obeznámeni s tímto procesem, můžete postup [načíst připojovací řetězec pomocí Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) ):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Další kroky

Teď, když máte nainstalovanou IoT Edge zařízení s nainstalovaným modulem runtime, můžete [nasadit IoT Edge moduly](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací hraničního modulu runtime, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Další informace o používání virtuálních počítačů s Windows najdete v [dokumentaci k Windows Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

Pokud chcete k tomuto virtuálnímu počítači po instalaci použít SSH, postupujte podle pokynů v části [instalace Průvodce OpenSSH pro Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) pomocí vzdálené plochy nebo vzdáleného prostředí PowerShell.
