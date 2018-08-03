---
title: Konfigurace odesílání souborů do IoT Hubu pomocí rozhraní příkazového řádku Azure (az.py) | Dokumentace Microsoftu
description: Jak nakonfigurovat fileuploads do služby Azure IoT Hub pomocí napříč platformami Azure CLI 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0eac620d44967827f7703da9cf409703a123ab07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460195"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurace centra IoT nahrávání souborů pomocí Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Použít [funkci nahrávání souborů ve službě IoT Hub][lnk-upload], je třeba nejprve přidružit účet Azure Storage pomocí služby IoT hub. Můžete použít existující účet úložiště nebo vytvořte novou.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Azure CLI 2.0][lnk-CLI-install].
* Služby Azure IoT hub. Pokud nemáte k dispozici služby IoT hub, můžete použít `az iot hub create` [příkaz] [ lnk-cli-create-iothub] vytvořit také pomocí portálu [vytvoření služby IoT hub] [lnk-portal-hub].
* Účet služby Azure Storage. Pokud nemáte účet Azure Storage, můžete použít [příkazového řádku Azure CLI 2.0 – Správa účtů úložiště] [ lnk-manage-storage] a vytvořte si ho pomocí portálu [vytvořit účet úložiště] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a v nastavení účtu Azure

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

## <a name="retrieve-your-storage-account-details"></a>Načíst podrobnosti o vašem účtu úložiště

Následující postup předpokládá, že jste vytvořili pomocí účtu úložiště **Resource Manageru** model nasazení a ne **Classic** modelu nasazení.

Pokud chcete nakonfigurovat nahrávání souborů ze zařízení, potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služby IoT hub. Potřebujete také název kontejneru objektů blob v účtu úložiště. Použijte následující příkaz k načtení klíčů účtů úložiště:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Poznamenejte si, **connectionString** hodnotu. Budete ho potřebovat v následujících krocích.

Můžete použít existující kontejner objektů blob pro vaše nahrávání souborů, nebo vytvořte nové:

* Pro zobrazení seznamu existující kontejnery objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Chcete-li vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Nahrání souboru

Teď můžete nakonfigurovat službu IoT hub umožňuje [funkcí odesílání souborů] [ lnk-upload] pomocí podrobnosti o vašem účtu úložiště.

Následující konfigurace vyžaduje následující hodnoty:

**Kontejner úložiště**: kontejner objektů blob v účtu služby Azure storage v aktuálním předplatném Azure pro přidružení k službě IoT hub. Který jste získali informace o účtu úložiště potřebné v předchozí části. IoT Hub automaticky generuje identifikátorů URI SAS s oprávněním pro zápis do tohoto kontejneru objektů blob pro zařízení se má použít při jejich nahrávání souborů.

**Přijímat oznámení o nahraných souborech**: Povolí nebo zakáže oznámení o nahrávání souborů.

**Hodnota TTL SAS**: Toto nastavení je, time-to-live identifikátorů URI SAS, které vrátí zařízení služby IoT Hub. Ve výchozím nastavení jedna hodina.

**Soubor oznámení, nastavení výchozí hodnota TTL**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den.

**Soubor oznámení maximální počet doručení**: počet, kolikrát se IoT Hub pokusí doručit do souboru odeslat oznámení. Ve výchozím nastavení má 10.

Pomocí následujících příkazů Azure CLI ke konfiguraci nastavení nahrávání souborů ve službě IoT hub:

Při použití prostředí bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Při použití příkazového řádku Windows:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfigurace odesílání souborů můžete zkontrolovat ve službě IoT hub pomocí následujícího příkazu:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Další postup

Další informace o možnostech nahrávání souborů služby IoT Hub najdete v tématu [nahrávání souborů ze zařízení][lnk-upload].

Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Hromadná Správa zařízení IoT][lnk-bulk]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub][lnk-devguide]
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
[lnk-securing]: /azure/iot-fundamentals/iot-security-ground-up


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
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create