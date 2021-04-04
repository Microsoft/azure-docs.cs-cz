---
title: Konfigurace nahrávání souborů pro IoT Hub pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Jak nakonfigurovat nahrávání souborů do Azure IoT Hub pomocí Azure CLI pro různé platformy.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536058"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurace nahrávání souborů IoT Hub pomocí Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Pokud chcete [nahrávat soubory ze zařízení](iot-hub-devguide-file-upload.md), musíte nejdřív přidružit účet Azure Storage ke službě IoT Hub. Můžete použít existující účet úložiště nebo vytvořit nový.

K dokončení tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)

* Azure IoT Hub. Pokud Centrum IoT nemáte, můžete pomocí [ `az iot hub create` příkazu](/cli/azure/iot/hub#az-iot-hub-create) vytvořit nebo [vytvořit centrum IoT pomocí portálu](iot-hub-create-through-portal.md).

* Účet služby Azure Storage. Pokud účet Azure Storage nemáte, můžete ho vytvořit pomocí rozhraní příkazového řádku Azure. Další informace najdete v článku o [vytvoření účtu úložiště](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte svůj účet Azure.

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku spusťte [příkaz pro přihlášení](/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure](/cli/azure/account), které můžete použít:

    ```azurecli
    az account list
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření centra IoT. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností účtu úložiště

Následující postup předpokládá, že jste vytvořili účet úložiště pomocí modelu nasazení **Správce prostředků** , a ne modelu nasazení **Classic** .

Ke konfiguraci nahrávání souborů z vašich zařízení potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služba IoT Hub. Také potřebujete název kontejneru objektů BLOB v účtu úložiště. Pomocí následujícího příkazu načtěte klíče účtu úložiště:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Poznamenejte si hodnotu **připojovacího řetězce** . Budete ho potřebovat v následujících krocích.

Můžete použít existující kontejner objektů BLOB pro nahrání souboru nebo vytvořit nový.

* Pokud chcete zobrazit seznam existujících kontejnerů objektů BLOB v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Pokud chcete ve svém účtu úložiště vytvořit kontejner objektů blob, použijte následující příkaz:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Nahrání souboru

Teď můžete nakonfigurovat centrum IoT, aby bylo možné [nahrávat soubory do služby IoT Hub](iot-hub-devguide-file-upload.md) s využitím podrobností účtu úložiště.

Konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: kontejner objektů BLOB v účtu služby Azure Storage v aktuálním předplatném Azure, který chcete přidružit ke službě IoT Hub. V předchozí části jste načetli potřebné informace o účtu úložiště. IoT Hub automaticky generuje identifikátory URI SAS s oprávněním k zápisu do tohoto kontejneru objektů blob, aby bylo možné zařízení použít při nahrávání souborů.

* **Dostávat oznámení pro nahrané soubory**: povolení nebo zakázání oznámení o nahrávání souborů

* **SAS TTL**: Toto nastavení představuje čas do živého vysílání identifikátorů URI SAS vrácených do zařízení IoT Hub. Ve výchozím nastavení nastavte na jednu hodinu.

* **Nastavení oznámení o souboru výchozí hodnota TTL**: čas do živého oznámení o nahrání souboru před vypršením jeho platnosti. Ve výchozím nastavení nastaveno na jeden den.

* **Maximální počet doručení oznámení souborů**: počet, kolikrát se IoT Hub pokusí doručovat oznámení o nahrání souboru. Ve výchozím nastavení nastavte na hodnotu 10.

Pomocí následujících příkazů rozhraní příkazového řádku Azure nakonfigurujte nastavení nahrávání souborů ve službě IoT Hub:

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

Konfiguraci nahrávání souborů ve službě IoT Hub můžete zkontrolovat pomocí tohoto příkazu:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech nahrávání souborů IoT Hub najdete v tématu [nahrání souborů ze zařízení](iot-hub-devguide-file-upload.md).

Pokud chcete získat další informace o správě IoT Hub Azure, postupujte podle těchto odkazů:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorování služby IoT Hub](monitor-iot-hub.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpečení řešení IoT od základů](../iot-fundamentals/iot-security-ground-up.md)