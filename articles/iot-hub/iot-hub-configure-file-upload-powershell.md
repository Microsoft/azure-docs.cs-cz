---
title: Konfigurace odesílání souborů pomocí Azure Powershellu | Dokumentace Microsoftu
description: Jak pomocí rutin Azure Powershellu ke konfiguraci služby IoT hub k povolení souborové ukládání z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: f468567aa67560eed972fb53669638a17778b307
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189950"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurace centra IoT nahrávání souborů pomocí Powershellu

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Použít [funkci nahrávání souborů ve službě IoT Hub](iot-hub-devguide-file-upload.md), je třeba nejprve přidružit účet úložiště Azure s centrem IoT. Můžete použít existující účet úložiště nebo vytvořte novou.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) během několika minut.

* [Rutiny Powershellu pro Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

* Služby Azure IoT hub. Pokud nemáte k dispozici služby IoT hub, můžete použít [rutiny New-AzureRmIoTHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub) a vytvořte si ho pomocí portálu [vytvoření služby IoT hub](iot-hub-create-through-portal.md).

* Účet úložiště Azure. Pokud nemáte účet úložiště Azure, můžete použít [rutin Powershellu pro Azure Storage](https://docs.microsoft.com/powershell/module/azurerm.storage/) a vytvořte si ho pomocí portálu [vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a v nastavení účtu Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku Powershellu, spusťte **Connect-AzureRmAccount** rutiny:

    ```powershell
    Connect-AzureRmAccount
    ```

2. Pokud máte více předplatných Azure, přihlášení k Azure získáte přístup ke Všechna předplatná Azure přidružená svoje přihlašovací údaje. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure k dispozici pro použití:

    ```powershell
    Get-AzureRMSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro správu služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Načíst podrobnosti o vašem účtu úložiště

Následující postup předpokládá, že jste vytvořili pomocí účtu úložiště **Resource Manageru** model nasazení a ne **Classic** modelu nasazení.

Pokud chcete nakonfigurovat nahrávání souborů ze zařízení, potřebujete připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služby IoT hub. Potřebujete také název kontejneru objektů blob v účtu úložiště. Použijte následující příkaz k načtení klíčů účtů úložiště:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Poznamenejte si, **key1** hodnotu klíče účtu úložiště. Budete ho potřebovat v následujících krocích.

Můžete použít existující kontejner objektů blob pro vaše nahrávání souborů, nebo vytvořte nové:

* K zobrazení seznamu existující kontejnery objektů blob v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Chcete-li vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurace centra IoT

Teď můžete nakonfigurovat službu IoT hub do [nahrání souborů do služby IoT hub](iot-hub-devguide-file-upload.md) pomocí podrobnosti o vašem účtu úložiště.

Následující konfigurace vyžaduje následující hodnoty:

* **Kontejner úložiště**: kontejner objektů blob v účtu služby Azure storage v aktuálním předplatném Azure pro přidružení k službě IoT hub. Který jste získali informace o účtu úložiště potřebné v předchozí části. IoT Hub automaticky generuje identifikátorů URI SAS s oprávněním pro zápis do tohoto kontejneru objektů blob pro zařízení se má použít při jejich nahrávání souborů.

* **Přijímat oznámení o nahraných souborech**: Povolí nebo zakáže oznámení o nahrávání souborů.

* **Hodnota TTL SAS**: Toto nastavení je, time-to-live identifikátorů URI SAS, které vrátí zařízení služby IoT Hub. Ve výchozím nastavení jedna hodina.

* **Soubor oznámení, nastavení výchozí hodnota TTL**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den.

* **Soubor oznámení maximální počet doručení**: počet, kolikrát se IoT Hub pokusí doručit do souboru odeslat oznámení. Ve výchozím nastavení má 10.

Pomocí následující rutiny prostředí PowerShell můžete nakonfigurovat soubor nahrát nastavení ve službě IoT hub:

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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