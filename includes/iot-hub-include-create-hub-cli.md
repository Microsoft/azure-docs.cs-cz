---
title: zahrnout soubor
description: zahrnout soubor
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 4999bd93f338ca7b34b141b88e06e4a769a4aaa1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876419"
---
V následujících částech jste nastavili terminál a pomocí Azure CLI vytvoříte službu IoT Hub. Pokud chcete nakonfigurovat terminál, který spouští příkazy rozhraní příkazového řádku Azure, můžete buď použít Azure Cloud Shell na základě prohlížeče, nebo použít místní terminál.
* Pokud chcete použít Cloud Shell, přečtěte si další část: [spuštění Cloud Shell](#launch-the-cloud-shell). 
* Chcete-li použít místní terminál, přeskočte další část a přejděte k [otevření místního terminálu](#open-a-local-terminal).

## <a name="launch-the-cloud-shell"></a>Spuštění Cloud Shell
V této části vytvoříte relaci Cloud Shell a konfigurujete své Terminálové prostředí.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.  

Spuštění Cloud Shell:

1. Vyberte tlačítko **Cloud Shell** v pravém horním panelu nabídek v Azure Portal. 

    ![Tlačítko Cloud Shell Azure Portal](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Pokud Cloud Shellte poprvé, zobrazí se výzva k vytvoření úložiště, které je nutné k použití Cloud Shell.  Vyberte předplatné, ve kterém chcete vytvořit účet úložiště a sdílenou složku Microsoft Azure Files. 

2. V rozevíracím seznamu **Vybrat prostředí** vyberte preferované prostředí rozhraní příkazového řádku. V tomto rychlém startu se používá prostředí **bash** . Všechny následující příkazy rozhraní příkazového řádku fungují i v prostředí PowerShellu. 

    ![Vybrat prostředí CLI](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Přeskočte další část a přejděte k části [Instalace rozšíření Azure IoT](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Otevření místního terminálu
Pokud jste se rozhodli použít místo Cloud Shell místní terminál, dokončete tuto část.  

1. Otevřete místní terminál.
1. Spusťte příkaz [AZ Login](/cli/azure/reference-index#az_login) :

   ```azurecli
   az login
   ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese https://aka.ms/devicelogin a zadejte autorizační kód zobrazený v terminálu.

    Pokud není k dispozici žádný webový prohlížeč nebo se nepovede otevřít webový prohlížeč, použijte tok kódu zařízení s `az login --use-device-code` .

1. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

    Další informace o různých metodách ověřování najdete v tématu [Přihlášení pomocí Azure CLI]( /cli/azure/authenticate-azure-cli ).

1. Přejít k další části: [Instalace rozšíření Azure IoT](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Instalace rozšíření Azure IoT
V této části nainstalujete Microsoft Azure rozšíření IoT pro Azure CLI do prostředí CLI. Rozšíření IOT přidá do Azure CLI příkazy určené pro služby IoT Hub, IoT Edge a IoT Device Provisioning Service (DPS).

> [!IMPORTANT]
> Příkazy terminálu ve zbývající části tohoto rychlého startu fungují stejně jako v Cloud Shell nebo v místním terminálu. Pokud chcete spustit příkaz, vyberte **Kopírovat** a zkopírujte blok kódu v tomto rychlém startu. Pak ho vložte do prostředí CLI a spusťte ho.

Spusťte příkaz [AZ Extension Add](/cli/azure/extension#az_extension_add) . 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT
V této části použijete Azure CLI k vytvoření centra IoT a skupiny prostředků.  Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. IoT Hub funguje jako centrální Centrum zpráv pro obousměrnou komunikaci mezi vaší aplikací IoT a zařízeními. 

Vytvoření centra IoT a skupiny prostředků:

1. Spuštěním příkazu [AZ Group Create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků. Následující příkaz vytvoří skupinu prostředků s názvem *MyResourceGroup* v umístění *eastus* . 
    >[!NOTE]
    > Volitelně můžete nastavit alternativní umístění. Chcete-li zobrazit dostupná umístění, spusťte příkaz `az account list-locations` . V tomto kurzu se používá *eastus* , jak je znázorněno v příkladu příkazu. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Spuštěním příkazu [AZ IoT Hub Create](/cli/azure/iot/hub#az_iot_hub_create) vytvořte centrum IoT. Vytvoření centra IoT může trvat několik minut. 

    *YourIotHubName*. Nahraďte tento zástupný text a okolní závorky v následujícím příkazu pomocí názvu, který jste zvolili pro Centrum IoT. Název centra IoT musí být v Azure globálně jedinečný. Pokud vidíte zástupný symbol, použijte název služby IoT Hub ve zbývající části tohoto rychlého startu.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení
V této části vytvoříte simulované zařízení IoT, které je připojené ke službě IoT Hub. 

Postup vytvoření simulovaného zařízení:
1. Spusťte příkaz [AZ IoT Hub Device-identity Create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) v prostředí CLI. Tím se vytvoří simulovaná identita zařízení. 

    *YourIotHubName*. Nahraďte tento zástupný symbol níže názvem, který jste zvolili pro Centrum IoT. 

    *mojezařízení*. Tento název můžete použít přímo pro ID simulovaného zařízení ve zbývající části tohoto článku. Volitelně použijte jiný název. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Spusťte příkaz [AZ IoT Hub Device-identity Connection-String show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) . 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Výstup připojovacího řetězce je v následujícím formátu:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Uložte připojovací řetězec do zabezpečeného umístění. 

> [!NOTE]
> Nechejte prostředí CLI otevřené. Použijete ho v pozdějších krocích.