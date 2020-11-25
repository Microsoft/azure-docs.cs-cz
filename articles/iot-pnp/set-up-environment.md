---
title: Nastavení prostředků IoT, které potřebujete pro IoT technologie Plug and Play | Microsoft Docs
description: Vytvořte IoT Hub a instanci služby Device Provisioning, kterou chcete použít, technologie Plug and Play rychlý Start a kurzy pro IoT.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6e6c090c02798103a30cc68f2ca28e8c4f7f2f17
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "96004258"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Nastavení prostředí pro technologie Plug and Play rychlý Start a kurzy pro IoT

Předtím, než budete moci dokončit technologie Plug and Play rychlých startů a kurzů pro IoT, musíte ve svém předplatném Azure nakonfigurovat službu IoT Hub a službu Device Provisioning (DPS). Budete také potřebovat místní kopie souborů modelů používaných ukázkovými aplikacemi a nástrojem Azure IoT Explorer.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

Abyste se vyhnuli nutnosti nainstalovat rozhraní příkazového řádku Azure CLI místně, můžete k nastavení Cloud Services použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Vytvoření prostředků

Vytvořte skupinu prostředků Azure pro prostředky:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Vytvořte centrum IoT. Následující příkaz používá název `my-pnp-hub` jako příklad pro název služby IoT Hub, který se má vytvořit. Vyberte jedinečný název, který má vaše centrum IoT použít místo `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Vytvořte instanci DPS. Následující příkaz používá název `my-pnp-dps` jako příklad pro název instance DPS, která se má vytvořit. Vyberte jedinečný název, který má vaše instance DPS použít místo `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

K propojení instance DPS s centrem IoT použijte následující příkazy. Nahraďte `my-pnp-dps` a `my-pnp-hub` s jedinečnými názvy, které jste si zvolili dříve:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Načíst nastavení

V některých rychlých startech a kurzech se používá připojovací řetězec pro Centrum IoT. Připojovací řetězec budete potřebovat také při nastavování nástroje Azure IoT Explorer. Načtěte připojovací řetězec a poznamenejte si ho hned teď. Nahraďte `my-pnp-hub` jedinečným názvem, který jste zvolili pro Centrum IoT:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

Většina rychlých startů a kurzů používá *Rozsah ID* konfigurace v DPS. Načtěte rozsah ID a poznamenejte si ho hned teď. Nahraďte `my-pnp-dps` jedinečným názvem, který jste zvolili pro instanci DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Všechny rychlé starty a kurzy používají registraci zařízení DPS. Pomocí následujícího příkazu vytvořte `my-pnp-device` v instanci DPS *individuální registraci zařízení* . Nahraďte `my-pnp-dps` jedinečným názvem, který jste zvolili pro instanci DPS. Poznamenejte si ID registrace a hodnoty primárního klíče, které se mají použít v rychlých startech a kurzech:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Vytvoření proměnných prostředí

Vytvořte pět proměnných prostředí pro konfiguraci ukázek v rychlých startech a kurzech k použití služby Device Provisioning (DPS) pro připojení ke službě IoT Hub:

* **IOTHUB_DEVICE_SECURITY_TYPE**: hodnota `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: obor ID DPS, na který jste si poznamenali dříve.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: hodnota `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: primární klíč registrace, na který jste si poznamenali předchozí poznámku.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: hodnota `global.azure-devices-provisioning.net`

Ukázky služeb potřebují k identifikaci centra a zařízení, ke kterému se má připojit, následující proměnné prostředí:

* **IOTHUB_CONNECTION_STRING**: připojovací řetězec ke službě IoT Hub jste si poznamenali dříve.
* **IOTHUB_DEVICE_ID**: `my-pnp-device` .

Například v prostředí Linux bash shell:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Například na příkazovém řádku systému Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Stažení souborů modelu

Rychlé starty a kurzy používají ukázkové soubory modelu pro kontrolku teploty a termostaty zařízení. Stažení ukázkových souborů modelu:

1. Vytvořte ve svém místním počítači složku s názvem *modely* .

1. Klikněte pravým tlačítkem na [TemperatureController.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) a uložte soubor JSON do složky *modely* .

1. Klikněte pravým tlačítkem na [Thermostat.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) a uložte soubor JSON do složky *modely* .

## <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Rychlé starty a kurzy využívají nástroj **Azure IoT Explorer** . V [Azure IoT Exploreru](https://github.com/Azure/azure-iot-explorer/releases) Vydáte verze a rozbalte seznam assetů pro nejnovější verzi. Stáhněte a nainstalujte si nejnovější verzi aplikace pro váš operační systém.

Při prvním spuštění nástroje budete vyzváni k zadání připojovacího řetězce služby IoT Hub. Použijte připojovací řetězec, který jste si poznamenali dříve.

Nakonfigurujte nástroj tak, aby používal soubory modelu, které jste si stáhli dříve. Z domovské stránky v nástroji vyberte **Nastavení IoT technologie Plug and Play** a potom **+ Přidat > místní složky**. Vyberte složku *modely* , kterou jste předtím vytvořili. Pak vyberte **Uložit** a uložte nastavení.

Další informace najdete v tématu [instalace a použití Azure IoT Exploreru](howto-use-iot-explorer.md).

## <a name="remove-the-resources"></a>Odebrat prostředky

K distechnologie Plug and Play rychlým startům a kurzům pro IoT můžete použít instanci služby IoT Hub a DPS, takže je potřeba provést pouze kroky v tomto článku. Až budete hotovi, můžete je z předplatného odebrat pomocí následujícího příkazu:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili prostředí, můžete vyzkoušet některý z rychlých startů nebo kurzů, jako je:

> [!div class="nextstepaction"]
> [Připojte k IoT Hub ukázkovou aplikaci IoT technologie Plug and Play zařízení.](quickstart-connect-device.md)
