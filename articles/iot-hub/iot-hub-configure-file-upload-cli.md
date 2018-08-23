---
title: Konfigurace odesílání souborů do IoT Hubu pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Jak nakonfigurovat soubor nahraje do služby Azure IoT Hub pomocí Azure CLI pro různé platformy.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6cd0b657c8d0352c41e0da538396b166d633306a
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058046"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurace centra IoT nahrávání souborů pomocí Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

K [nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md), je třeba nejprve přidružit účet Azure Storage pomocí služby IoT hub. Můžete použít existující účet úložiště nebo vytvořte novou.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Služby Azure IoT hub. Pokud nemáte k dispozici služby IoT hub, můžete použít [ `az iot hub create` příkaz](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) nějaké nebo [vytvoření IoT hubu pomocí portálu](iot-hub-create-through-portal.md).

* Účet služby Azure Storage. Pokud nemáte účet Azure Storage, můžete použít [Azure CLI – Správa účtů úložiště](../storage/common/storage-azure-cli.md#manage-storage-accounts) a vytvořte si ho pomocí portálu [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a v nastavení účtu Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku, spusťte [přihlašovací příkaz](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujících [příkazu zobrazte výpis účtů Azure](https://docs.microsoft.com/cli/azure/account) k dispozici pro použití:

    ```azurecli
    az account list
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Načíst podrobnosti o vašem účtu úložiště

Následující postup předpokládá, že jste vytvořili pomocí účtu úložiště **Resource Manageru** model nasazení a ne **Classic** modelu nasazení.

Pokud chcete nakonfigurovat nahrávání souborů ze zařízení, potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služby IoT hub. Potřebujete také název kontejneru objektů blob v účtu úložiště. Použijte následující příkaz k načtení klíčů účtů úložiště:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Poznamenejte si, **connectionString** hodnotu. Budete ho potřebovat v následujících krocích.

Můžete použít existující kontejner objektů blob pro vaše nahrávání souborů, nebo vytvořte nové:

* Pro zobrazení seznamu existující kontejnery objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Chcete-li vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Nahrání souboru

Teď můžete nakonfigurovat službu IoT hub povolit možnost [nahrání souborů do služby IoT hub](iot-hub-devguide-file-upload.md) pomocí podrobnosti o vašem účtu úložiště.

Následující konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: kontejner objektů blob v účtu služby Azure storage v aktuálním předplatném Azure pro přidružení k službě IoT hub. Který jste získali informace o účtu úložiště potřebné v předchozí části. IoT Hub automaticky generuje identifikátorů URI SAS s oprávněním pro zápis do tohoto kontejneru objektů blob pro zařízení se má použít při jejich nahrávání souborů.

* **Přijímat oznámení o nahraných souborech**: Povolí nebo zakáže oznámení o nahrávání souborů.

* **Hodnota TTL SAS**: Toto nastavení je, time-to-live identifikátorů URI SAS, které vrátí zařízení služby IoT Hub. Ve výchozím nastavení jedna hodina.

* **Soubor oznámení, nastavení výchozí hodnota TTL**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den.

* **Soubor oznámení maximální počet doručení**: počet, kolikrát se IoT Hub pokusí doručit do souboru odeslat oznámení. Ve výchozím nastavení má 10.

Pomocí následujících příkazů Azure CLI ke konfiguraci nastavení nahrávání souborů ve službě IoT hub:

<!--Robinsh this is out of date, add cloud powershell -->

V prostředí bash použijte:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfigurace odesílání souborů můžete zkontrolovat ve službě IoT hub pomocí následujícího příkazu:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Další postup

Další informace o možnostech nahrávání souborů služby IoT Hub najdete v tématu [nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md).

Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpečení řešení IoT od základů nahoru](../iot-fundamentals/iot-security-ground-up.md)
