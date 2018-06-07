---
title: Konfigurace nahrávání souborů do služby IoT Hub pomocí rozhraní příkazového řádku Azure (az.py) | Microsoft Docs
description: Postup konfigurace fileuploads ke službě Azure IoT Hub pomocí Azure CLI a platformy 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 378fb06f7ac9cbb6dc645994682786f474077d03
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633848"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurace centra IoT nahrávání souborů pomocí rozhraní příkazového řádku Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Použít [souboru nahrávání funkce IoT hub][lnk-upload], je třeba nejprve přidružit účet služby Azure Storage pomocí služby IoT hub. Můžete použít existující účet úložiště nebo vytvořte novou.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Rozhraní příkazového řádku Azure 2.0][lnk-CLI-install].
* Centrum Azure IoT. Pokud nemáte Centrum IoT, můžete použít `az iot hub create` [příkaz] [ lnk-cli-create-iothub] vytvořit nebo použít na portálu pro [vytvoření služby IoT hub] [lnk-portal-hub].
* Účet služby Azure Storage. Pokud nemáte účet úložiště Azure, můžete použít [Azure CLI 2.0 - spravovat účty úložiště] [ lnk-manage-storage] vytvořit nebo použít portálu [vytvořit účet úložiště][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavit váš účet Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku spusťte [příkaz pro přihlášení][lnk-login-command]:

    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

1. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure][lnk-az-account-command], které můžete použít:

    ```azurecli
    az account list
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření centra IoT. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností o účtu úložiště

Následující postup předpokládá, že jste vytvořili pomocí účtu úložiště **Resource Manager** model nasazení a ne **Classic** modelu nasazení.

Pokud chcete konfigurovat nahrávání souborů ze zařízení, musíte připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služby IoT hub. Musíte také název kontejneru objektů blob v účtu úložiště. Použijte následující příkaz k načtení klíče účtu úložiště:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Poznamenejte si **connectionString** hodnotu. Musíte ho v následujících krocích.

Můžete použít existující kontejner objektů blob pro vaše nahrávání souborů nebo vytvořte nové:

* K zobrazení seznamu existující kontejnery objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Nahrání souboru

Teď můžete nakonfigurovat službu IoT hub, chcete-li povolit [souboru nahrávání funkce] [ lnk-upload] pomocí údaje o vašem účtu úložiště.

Následující konfigurace vyžaduje následující hodnoty:

**Kontejner úložiště**: kontejner objektů blob v účtu úložiště Azure v aktuálním předplatném Azure přidružit služby IoT hub. Můžete načíst informace o účtu potřeby úložiště v předchozí části. IoT Hub automaticky vygeneruje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob pro zařízení se má použít při odesílají soubory.

**Přijímat oznámení pro nahraném soubory**: povolení nebo zakázání oznámení o odeslání souboru.

**Hodnota TTL SAS**: Toto nastavení je time-to-live identifikátorů URI SAS, vrácený do zařízení IoT Hub. Ve výchozím nastavení má jednu hodinu.

**Upozornění na nastavení výchozí hodnota TTL souboru**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den.

**Soubor doručení maximální počet oznámení**: počet, kolikrát se Centrum IoT pokusí doručit soubor odeslat oznámení. Ve výchozím nastavení má 10.

Pomocí následujících příkazů příkazového řádku Azure CLI ke konfiguraci nastavení nahrávání souborů ve službě IoT hub:

K využívání prostředí bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Na Windows použijte příkazový řádek:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfigurace nahrávání souborů můžete zkontrolovat ve službě IoT hub pomocí následujícího příkazu:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Další postup

Další informace o možnostech nahrávání souboru Centrum IoT najdete v tématu [nahrání souborů ze zařízení][lnk-upload].

Další informace o správě Azure IoT Hub na následujících odkazech:

* [Hromadné spravovat zařízení IoT][lnk-bulk]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]
* [Zabezpečení řešení IoT od základů nahoru][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create