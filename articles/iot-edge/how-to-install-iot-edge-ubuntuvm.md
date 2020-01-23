---
title: Spustit Azure IoT Edge v Virtual Machines Ubuntu | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge Azure Marketplace Ubuntu 16,04 Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 84dd1b6d657dfe5061f53698114f3dc4e0197571
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510052"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Spuštění Azure IoT Edge v Ubuntu Virtual Machines

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu.

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky, jak spustit modul runtime Azure IoT Edge na virtuálním počítači s Ubuntu 16,04 pomocí předkonfigurované [Azure IoT Edge na Ubuntu Azure Marketplace nabídky](https://aka.ms/azure-iot-edge-ubuntuvm).

Při prvním spuštění předinstaluje Azure IoT Edge na virtuálním počítači s Ubuntu nejnovější verzi Azure IoT Edge runtime. Obsahuje také skript pro nastavení připojovacího řetězce a následné restartování modulu runtime, který může být aktivován vzdáleně prostřednictvím portálu virtuálního počítače Azure nebo pomocí příkazového řádku Azure, který umožňuje snadnou konfiguraci a připojení zařízení IoT Edge bez spuštění protokolu SSH nebo vzdáleného přístupu. relace plochy. Tento skript počká na nastavení připojovacího řetězce, dokud nebude klient IoT Edge plně nainstalován, takže nemusíte ho sestavovat do automatizace.

## <a name="deploy-from-the-azure-marketplace"></a>Nasazení z Azure Marketplace

1. Přejděte do [Azure IoT Edge nabídky na webu Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace nebo na [Azure Marketplace](https://azuremarketplace.microsoft.com/) Azure IoT Edge na Ubuntu.
2. Vyberte **získat** a potom **pokračujte** v dalším dialogovém okně.
3. V Azure Portal vyberte **vytvořit** a postupujte podle pokynů průvodce a NASAĎTE virtuální počítač.
    * Pokud si virtuální počítač vyzkoušíte poprvé, je jednodušší použít heslo a povolit SSH v nabídce veřejného příchozího portu.
    * Pokud máte úlohu náročné na prostředky, měli byste upgradovat velikost virtuálního počítače přidáním dalších procesorů nebo paměti.
4. Po nasazení virtuálního počítače ho nakonfigurujte tak, aby se připojil k vašemu IoT Hub:
    1. Z IoT Edge zařízení vytvořeného v IoT Hub zkopírujte připojovací řetězec zařízení. Pokud tento proces neznáte, můžete postupovat podle pokynů k [Načtení připojovacího řetězce v Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) .
    1. Vyberte nově vytvořený prostředek virtuálního počítače z Azure Portal a otevřete možnost **Spustit příkaz** .
    1. Vyberte možnost **RunShellScript**
    1. Spusťte skript níže prostřednictvím příkazového okna s připojovacím řetězcem zařízení: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1. Vyberte **Spustit** .
    1. Chvíli počkejte a obrazovka by pak měla poskytnout zprávu o úspěchu, která indikuje, že se připojovací řetězec úspěšně nastavil.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z Azure Portal

V Azure Portal vyhledejte "Azure IoT Edge" a vyberte **Ubuntu Server 16,04 LTS + Azure IoT Edge runtime** pro zahájení pracovního postupu vytváření virtuálních počítačů. Odtud proveďte kroky 3 a 4 v části nasazení z Azure Marketplace výše.

## <a name="deploy-from-azure-cli"></a>Nasazení z Azure CLI

1. Pokud na ploše používáte rozhraní příkazového řádku Azure CLI, začněte přihlášením:

   ```azurecli-interactive
   az login
   ```

1. Pokud máte více předplatných, vyberte předplatné, které chcete použít:
   1. Výpis předplatných:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Zkopírujte pole SubscriptionID pro předplatné, které chcete použít.

   1. Nastavte své pracovní předplatné s ID, které jste právě zkopírovali:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Vytvořte novou skupinu prostředků (nebo v dalších krocích určete existující):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Přijměte podmínky použití pro virtuální počítač. Pokud si chcete nejdřív projít tyto výrazy, postupujte podle kroků v [části nasazení z Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Vytvořit nový virtuální počítač:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Nastavení připojovacího řetězce zařízení (Pokud nejste obeznámeni s tímto procesem, můžete postupovat podle postupu [Načtení připojovacího řetězce pomocí Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) ):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Pokud chcete k tomuto virtuálnímu počítači po instalaci použít SSH, použijte publicIpAddress s příkazem: `ssh azureuser@{publicIpAddress}`

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací modulu runtime IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

Pokud chcete otevřít porty pro přístup k virtuálnímu počítači přes SSH nebo jiná příchozí připojení, přečtěte si dokumentaci k virtuálnímu počítači Azure o [otevření portů a koncových bodů na virtuálním počítači se systémem Linux](../virtual-machines/linux/nsg-quickstart.md) .
