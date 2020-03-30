---
title: Konfigurace nahrávání souborů do služby IoT Hub pomocí rozhraní příkazového příkazu Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat nahrávání souborů do služby Azure IoT Hub pomocí rozhraní příkazového příkazu Azure pro různé platformy.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302521"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurace odesílání souborů služby IoT Hub pomocí rozhraní příkazového příkazového příkazu Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Chcete-li [nahrát soubory ze zařízení](iot-hub-devguide-file-upload.md), musíte nejprve přidružit účet Služby Azure Storage k centru IoT. Můžete použít existující účet úložiště nebo vytvořit nový.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Azure IoT hub. Pokud nemáte službu IoT hub, můžete pomocí [ `az iot hub create` příkazu](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) vytvořit jeden nebo [vytvořit službu IoT hub pomocí portálu](iot-hub-create-through-portal.md).

* Účet služby Azure Storage. Pokud nemáte účet Azure Storage, můžete použít Azure CLI k jeho vytvoření. Další informace najdete v článku o [vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte si účet Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku spusťte [příkaz pro přihlášení](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure](https://docs.microsoft.com/cli/azure/account), které můžete použít:

    ```azurecli
    az account list
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů k vytvoření služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností o účtu úložiště

Následující kroky předpokládají, že jste vytvořili účet úložiště pomocí modelu nasazení **Resource Manager,** a nikoli **model** klasického nasazení.

Chcete-li nakonfigurovat nahrávání souborů ze zařízení, potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako vaše služby IoT hub. Potřebujete také název kontejneru objektů blob v účtu úložiště. Pomocí následujícího příkazu načtěte klíče účtu úložiště:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Poznamenejte si hodnotu **connectionString.** Potřebujete ji v následujících krocích.

Pro nahrávání souborů můžete použít existující kontejner objektů blob nebo vytvořit nový:

* Chcete-li v ypsit existující kontejnery objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkaz:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Nahrání souboru

Teď můžete nakonfigurovat službu IoT hub tak, aby umožňovala [nahrávání souborů do služby IoT hub](iot-hub-devguide-file-upload.md) pomocí podrobností o účtu úložiště.

Konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: Kontejner objektů blob v účtu úložiště Azure v aktuálním předplatném Azure, který chcete přidružit k vašemu centru IoT hub. Načetli jste potřebné informace o účtu úložiště v předchozí části. IoT Hub automaticky generuje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob, aby je zařízení používala při nahrávání souborů.

* **Příjem oznámení pro nahrané soubory**: Povolení nebo zakázání oznámení o nahrávání souborů.

* **TTL SAS**: Toto nastavení je čas emitované identifikátory URI SAS vrácené do zařízení službou IoT Hub. Ve výchozím nastavení je nastavena na jednu hodinu.

* **Nastavení oznámení souboru výchozí TTL**: Doba-k-live oznámení o nahrání souboru před vypršením jeho platnosti. Ve výchozím nastavení nastavena na jeden den.

* **Maximální počet doručení oznámení souboru**: Počet pokusů centra IoT Hub doručit oznámení o nahrání souboru. Ve výchozím nastavení je nastaveno na hodnotu 10.

Ke konfiguraci nastavení nahrávání souborů v centru IoT hub použijte následující příkazy rozhraní příkazového příkazu Azure:

<!--Robinsh this is out of date, add cloud powershell -->

V bash shell, použijte:

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

Konfiguraci nahrávání souborů v centru IoT můžete zkontrolovat pomocí následujícího příkazu:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech nahrávání souborů v centru IoT Hub najdete [v tématu Nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md).

Další informace o správě služby Azure IoT Hub najdete na těchto odkazech:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriky IoT Hubu](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpečte své řešení IoT od základů](../iot-fundamentals/iot-security-ground-up.md)
