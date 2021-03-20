---
title: Pomocí Azure PowerShell nakonfigurujte nahrávání souborů | Microsoft Docs
description: Jak pomocí rutin Azure PowerShell nakonfigurovat službu IoT Hub, která umožňuje nahrávání souborů z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536007"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurace nahrávání souborů IoT Hub pomocí prostředí PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Pokud chcete používat [funkci nahrávání souborů v IoT Hub](iot-hub-devguide-file-upload.md), musíte nejdřív přidružit účet Azure Storage ke službě IoT Hub. Můžete použít existující účet úložiště nebo vytvořit nový.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* [Azure PowerShell rutiny](/powershell/azure/install-Az-ps).

* Azure IoT Hub. Pokud Centrum IoT nemáte, můžete k vytvoření [služby IoT Hub](iot-hub-create-through-portal.md)použít [rutinu New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) a vytvořit ji pomocí tohoto portálu.

* Účet úložiště Azure. Pokud nemáte účet úložiště Azure, můžete k vytvoření [účtu úložiště](../storage/common/storage-account-create.md) použít [rutiny Azure Storage PowerShellu](/powershell/module/az.storage/) nebo použít portál.

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte svůj účet Azure.

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku PowerShellu spusťte rutinu **Connect-AzAccount** :

    ```powershell
    Connect-AzAccount
    ```

2. Pokud máte několik předplatných Azure, přihlaste se k Azure, abyste měli přístup ke všem předplatným Azure přidruženým k vašim přihlašovacím údajům. K vypsání předplatných Azure, která můžete použít, použijte následující příkaz:

    ```powershell
    Get-AzSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro správu služby IoT Hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností účtu úložiště

Následující postup předpokládá, že jste vytvořili účet úložiště pomocí modelu nasazení **Správce prostředků** , a ne modelu nasazení **Classic** .

Ke konfiguraci nahrávání souborů z vašich zařízení potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služba IoT Hub. Také potřebujete název kontejneru objektů BLOB v účtu úložiště. Pomocí následujícího příkazu načtěte klíče účtu úložiště:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Poznamenejte si hodnotu klíče účtu úložiště **klíč1** . Budete ho potřebovat v následujících krocích.

Pro nahrání souboru můžete použít existující kontejner objektů BLOB nebo vytvořit nové:

* Pokud chcete zobrazit seznam existujících kontejnerů objektů BLOB v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Pokud chcete ve svém účtu úložiště vytvořit kontejner objektů blob, použijte následující příkazy:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurace centra IoT

Teď můžete nakonfigurovat centrum IoT pro [nahrávání souborů do služby IoT Hub](iot-hub-devguide-file-upload.md) s využitím podrobností účtu úložiště.

Konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: kontejner objektů BLOB v účtu služby Azure Storage v aktuálním předplatném Azure, který chcete přidružit ke službě IoT Hub. V předchozí části jste načetli potřebné informace o účtu úložiště. IoT Hub automaticky generuje identifikátory URI SAS s oprávněním k zápisu do tohoto kontejneru objektů blob, aby bylo možné zařízení použít při nahrávání souborů.

* **Dostávat oznámení pro nahrané soubory**: povolení nebo zakázání oznámení o nahrávání souborů

* **SAS TTL**: Toto nastavení představuje čas do živého vysílání identifikátorů URI SAS vrácených do zařízení IoT Hub. Ve výchozím nastavení nastavte na jednu hodinu.

* **Nastavení oznámení o souboru výchozí hodnota TTL**: čas do živého oznámení o nahrání souboru před vypršením jeho platnosti. Ve výchozím nastavení nastaveno na jeden den.

* **Maximální počet doručení oznámení souborů**: počet, kolikrát se IoT Hub pokusí doručovat oznámení o nahrání souboru. Ve výchozím nastavení nastavte na hodnotu 10.

Pomocí následující rutiny prostředí PowerShell nakonfigurujte nastavení nahrávání souborů ve službě IoT Hub:

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

Další informace o možnostech nahrávání souborů IoT Hub najdete v tématu [nahrání souborů ze zařízení](iot-hub-devguide-file-upload.md).

Pokud chcete získat další informace o správě IoT Hub Azure, postupujte podle těchto odkazů:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorování služby IoT Hub](monitor-iot-hub.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpečení řešení IoT od základů](../iot-fundamentals/iot-security-ground-up.md)