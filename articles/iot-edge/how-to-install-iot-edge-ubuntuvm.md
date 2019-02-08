---
title: Spusťte Azure IoT Edge na virtuálních počítačích se systémem Ubuntu | Dokumentace Microsoftu
description: Azure IoT Edge pokyny k instalaci na Ubuntu 16.04 Marketplace virtuálních počítačích Azure
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: gregman
ms.openlocfilehash: 7ff7671425e2a2a5dbebe2d09cadb8ef71bc7c97
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896664"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Spusťte Azure IoT Edge na virtuálních počítačích se systémem Ubuntu

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu.

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky ke spuštění modulu runtime Azure IoT Edge na Ubuntu 16.04 virtuálního počítače s využitím předem nakonfigurovaného [Azure IoT Edge na Ubuntu Azure Marketplace nabídku](http://aka.ms/azure-iot-edge-ubuntuvm). 

Při prvním spuštění provede Azure IoT Edge na virtuální počítač s Ubuntu předinstalaci nejnovější verzi modulu runtime Azure IoT Edge. Obsahuje také skript, který nastavíme připojovací řetězec a potom restartujte modul runtime, který se dá spouštět vzdáleně prostřednictvím virtuálního počítače Azure pomocí portálu nebo rozhraní příkazového řádku Azure, díky tomu můžete snadno nakonfigurovat a bez spuštění SSH nebo vzdálené připojení zařízení IoT Edge relace plochy. Tento skript počká, dokud klienta IoT Edge je plně nainstalovaná, takže není nutné do vaší automatizace nastavit připojovací řetězec.

## <a name="deploy-from-the-azure-marketplace"></a>Nasazení na webu Azure Marketplace
1.  Přejděte [Azure IoT Edge na Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) nabídky Marketplace nebo tak, že na "Azure IoT Edge na Ubuntu" [na webu Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Vyberte **získat** a potom **pokračovat** v dalším dialogovém okně.
3.  Jednou na webu Azure Portal, vyberte **vytvořit** a postupujte podle pokynů průvodce k nasazení virtuálního počítače. 
    *   Pokud je poprvé vyzkoušet si virtuální počítač, je nejjednodušší použití hesla a chcete povolit SSH v nabídce veřejného portu pro příchozí spojení. 
    *   Pokud nemáte úlohy náročné na prostředek, měli byste upgradovat velikost virtuálního počítače tak, že přidáte víc procesorů nebo paměti.
4.  Po nasazení virtuálního počítače, nakonfigurujte ji k připojení ke službě IoT Hub:
    1.  Zkopírujte připojovací řetězec zařízení ze zařízení IoT Edge vytvořené ve službě IoT Hub (můžete použít [zaregistrovat nová zařízení Azure IoT Edge z portálu Azure portal](how-to-register-device-portal.md) příručce s postupy, pokud nejste obeznámeni s tímto procesem)
    1.  Vyberte prostředek nově vytvořenému virtuálnímu počítači z webu Azure portal a otevřete **spusťte příkaz** možnost
    1.  Vyberte **RunShellScript** možnost
    1.  Spusťte níže uvedený skript prostřednictvím příkazového okna vaším připojovacím řetězcem zařízení: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Vyberte **spuštění**
    1.  Chvíli počkejte a na obrazovce by měla zadejte, že zpráva úspěch, připojovací řetězec byl úspěšně nastaven.


## <a name="deploy-from-the-azure-portal"></a>Nasazení na webu Azure Portal
Na webu Azure Portal, vyhledejte "Azure IoT Edge" a vyberte **Ubuntu Server 16.04 LTS a modul runtime Azure IoT Edge** zahájíte pracovní postup vytvoření virtuálního počítače. Odtud proveďte kroky 3 a 4 ve výše uvedených pokynů "Nasazení z Azure Marketplace".

## <a name="deploy-from-azure-cli"></a>Nasazení z příkazového řádku Azure
1.  Pokud je to poprvé nasazení virtuálního počítače z příkazového řádku, budete muset povolit programové nasazení u předplatného Azure:
    1. Otevřít [Azure IoT Edge na Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) nabídky Marketplace
    1. Vyberte **získat** a **pokračovat** na následující dialogové okno
    1. Vyberte **chcete nasadit prostřednictvím kódu programu? Začínáme** v dolní části dialogového okna portálu
    1. Klikněte na **povolit** tlačítko **konfigurovat programové nasazení** stránce a potom klikněte na **uložit**
1.  Pokud používáte rozhraní příkazového řádku Azure na pracovní ploše, začněte přihlášením:

    ```azurecli-interactive
    az login
    ```
    
1.  Pokud máte více předplatných, vyberte předplatné, které chcete použít:
    1.  Seznam vašich předplatných:
    
       ```azurecli-interactive
       azure account list --output table
       ```
    
    1.  Kopírování z pole ID předplatného pro předplatné, které chcete použít
    1.  S ID, které jste zkopírovali, spusťte tento příkaz:
    
       ```azurecli-interactive 
       az account set -s {SubscriptionId}
       ```
    
1.  Vytvořte novou skupinu prostředků (nebo zadejte existující v dalších krocích):

    ```azurecli-interactive
    az group create --name IoTEdgeResources --location westus2
    ```
    
1.  Vytvoření nového virtuálního počítače:

    ```azurecli-interactive
    az vm create --resource-group IoTEdgeResources --name EdgeVM –image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
    ```

1.  Nastavit připojovací řetězec zařízení (provedením [pomocí Azure CLI zaregistrujte nové zařízení Azure IoT Edge](how-to-register-device-cli.md) příručce s postupy, pokud nejste obeznámeni s tímto procesem):

    ```azurecli-interactive
    az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
    ```

Pokud chcete se SSH do tohoto virtuálního počítače po dokončení instalace, použití publicIpAddress pomocí příkazu: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s modulu runtime Edge instalaci správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

Aktualizace stávající instalace na nejnovější verzi služby IoT Edge, najdete v článku [aktualizovat démon zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
