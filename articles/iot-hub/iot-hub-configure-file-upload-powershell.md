---
title: Konfigurace nahrávání souborů pomocí Azure PowerShellu | Dokumenty společnosti Microsoft
description: Jak pomocí rutin Azure PowerShellu nakonfigurovat centrum IoT pro povolení nahrávání souborů z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60318437"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurace nahrávání souborů služby IoT Hub pomocí Prostředí PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Chcete-li použít [funkci nahrávání souborů v iot hubu](iot-hub-devguide-file-upload.md), musíte nejprve přidružit účet úložiště Azure s centrem IoT Hub. Můžete použít existující účet úložiště nebo vytvořit nový.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* [Rutiny prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Azure IoT hub. Pokud nemáte centrum IoT, můžete použít [rutinu New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) k vytvoření jednoho nebo pomocí portálu k [vytvoření centra IoT](iot-hub-create-through-portal.md)hub .

* Účet úložiště Azure. Pokud nemáte účet úložiště Azure, můžete použít [rutiny Azure Storage PowerShell](https://docs.microsoft.com/powershell/module/az.storage/) k vytvoření jednoho nebo pomocí portálu k [vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte si účet Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na výzvu prostředí PowerShell spusťte rutinu **Connect-AzAccount:**

    ```powershell
    Connect-AzAccount
    ```

2. Pokud máte více předplatných Azure, přihlášení k Azure vám uděluje přístup ke všem předplatným Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure, která můžete použít:

    ```powershell
    Get-AzSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro správu služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností o účtu úložiště

Následující kroky předpokládají, že jste vytvořili účet úložiště pomocí modelu nasazení **Resource Manager,** a nikoli **model** klasického nasazení.

Chcete-li nakonfigurovat nahrávání souborů ze zařízení, potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako vaše služby IoT hub. Potřebujete také název kontejneru objektů blob v účtu úložiště. Pomocí následujícího příkazu načtěte klíče účtu úložiště:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Poznamenejte si hodnotu klíče účtu úložiště **key1.** Potřebujete ji v následujících krocích.

Pro nahrávání souborů můžete použít existující kontejner objektů blob nebo vytvořit nový:

* Chcete-li vypsat existující kontejnery objektů blob v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurace centra IoT hub

Teď můžete nakonfigurovat službu IoT hub tak, aby [nahrávala soubory do služby IoT hub](iot-hub-devguide-file-upload.md) pomocí podrobností o účtu úložiště.

Konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: Kontejner objektů blob v účtu úložiště Azure v aktuálním předplatném Azure, který chcete přidružit k vašemu centru IoT hub. Načetli jste potřebné informace o účtu úložiště v předchozí části. IoT Hub automaticky generuje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob, aby je zařízení používala při nahrávání souborů.

* **Příjem oznámení pro nahrané soubory**: Povolení nebo zakázání oznámení o nahrávání souborů.

* **TTL SAS**: Toto nastavení je čas emitované identifikátory URI SAS vrácené do zařízení službou IoT Hub. Ve výchozím nastavení je nastavena na jednu hodinu.

* **Nastavení oznámení souboru výchozí TTL**: Doba-k-live oznámení o nahrání souboru před vypršením jeho platnosti. Ve výchozím nastavení nastavena na jeden den.

* **Maximální počet doručení oznámení souboru**: Počet pokusů centra IoT Hub doručit oznámení o nahrání souboru. Ve výchozím nastavení je nastaveno na hodnotu 10.

Pomocí následující rutiny Prostředí PowerShell nakonfigurujte nastavení nahrávání souborů v centru IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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
