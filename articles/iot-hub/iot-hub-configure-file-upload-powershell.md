---
title: Pomocí prostředí Azure PowerShell ke konfiguraci nahrávání souborů | Microsoft Docs
description: Postup použití rutin prostředí Azure PowerShell ke konfiguraci služby IoT hub, aby soubor ukládání z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a4a52b6a028f4c656404e90fe05f201ac77204d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631849"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurace centra IoT nahrávání souborů pomocí prostředí PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Použít [souboru nahrávání funkce IoT hub][lnk-upload], je třeba nejprve přidružit účet úložiště Azure službou IoT hub. Můžete použít existující účet úložiště nebo vytvořte novou.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Rutiny Azure PowerShell][lnk-powershell-install].
* Centrum Azure IoT. Pokud nemáte Centrum IoT, můžete použít [rutiny New-AzureRmIoTHub] [ lnk-powershell-iothub] vytvořit nebo použít portálu [vytvoření služby IoT hub][lnk-portal-hub].
* Účet úložiště Azure. Pokud nemáte účet úložiště Azure, můžete použít [rutin Azure Powershellu úložiště] [ lnk-powershell-storage] vytvořit nebo použít portálu [vytvořit účet úložiště][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavit váš účet Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. V řádku prostředí PowerShell, spusťte **Connect-AzureRmAccount** rutiny:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Pokud máte víc předplatných Azure, přihlášení do Azure uděluje přístup do všech předplatná Azure přidružená přihlašovacích údajů. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure, které je k dispozici pro použití:

    ```powershell
    Get-AzureRMSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů ke správě služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Načtení podrobností o účtu úložiště

Následující postup předpokládá, že jste vytvořili pomocí účtu úložiště **Resource Manager** model nasazení a ne **Classic** modelu nasazení.

Pokud chcete konfigurovat nahrávání souborů ze zařízení, musíte připojovací řetězec pro účet úložiště Azure. Účet úložiště musí být ve stejném předplatném jako služby IoT hub. Musíte také název kontejneru objektů blob v účtu úložiště. Použijte následující příkaz k načtení klíče účtu úložiště:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Poznamenejte si **key1** hodnotu klíče účtu úložiště. Musíte ho v následujících krocích.

Můžete použít existující kontejner objektů blob pro vaše nahrávání souborů nebo vytvořte nové:

* K zobrazení seznamu existující kontejnery objektů blob v účtu úložiště, použijte následující příkazy:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Pokud chcete vytvořit kontejner objektů blob v účtu úložiště, použijte následující příkazy:

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

Teď můžete nakonfigurovat službu IoT hub, chcete-li povolit [souboru nahrávání funkce] [ lnk-upload] pomocí údaje o vašem účtu úložiště.

Následující konfigurace vyžaduje následující hodnoty:

**Kontejner úložiště**: kontejner objektů blob v účtu úložiště Azure v aktuálním předplatném Azure přidružit služby IoT hub. Můžete načíst informace o účtu potřeby úložiště v předchozí části. IoT Hub automaticky vygeneruje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob pro zařízení se má použít při odesílají soubory.

**Přijímat oznámení pro nahraném soubory**: povolení nebo zakázání oznámení o odeslání souboru.

**Hodnota TTL SAS**: Toto nastavení je time-to-live identifikátorů URI SAS, vrácený do zařízení IoT Hub. Ve výchozím nastavení má jednu hodinu.

**Upozornění na nastavení výchozí hodnota TTL souboru**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den.

**Soubor doručení maximální počet oznámení**: počet, kolikrát se Centrum IoT pokusí doručit soubor odeslat oznámení. Ve výchozím nastavení má 10.

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

Další informace o možnostech nahrávání souboru Centrum IoT najdete v tématu [nahrání souborů ze zařízení][lnk-upload].

Další informace o správě Azure IoT Hub na následujících odkazech:

* [Hromadné spravovat zařízení IoT][lnk-bulk]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]
* [Zabezpečení řešení IoT od základů nahoru][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
