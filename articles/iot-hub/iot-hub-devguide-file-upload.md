---
title: Vysvětlení nahrávání souborů Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – použijte funkci odesílání souborů služby IoT Hub ke správě nahrávání souborů ze zařízení do kontejneru objektů blob v Azure storage.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: dobett
ms.openlocfilehash: 724f655229347aa73747476e6bb4837b30e0fc4f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839002"
---
# <a name="upload-files-with-iot-hub"></a>Nahrávání souborů pomocí služby IoT Hub

Jak [koncové body IoT Hubu](iot-hub-devguide-endpoints.md) článku, zařízení můžete spustit nahrávání souborů pomocí odesílání oznámení prostřednictvím koncového bodu připojeného k zařízení (**/devices/ {deviceId} / soubory**). Když zařízení oznámí služby IoT Hub, nahrání je dokončena, IoT Hub odešle zprávu s oznámením nahrávání souboru prostřednictvím **/messages/servicebound/filenotifications** koncového bodu připojeného k služby.

Místo zprostředkovatelské zpráv prostřednictvím služby IoT Hub, samotné služby IoT Hub místo funguje jako dispečer do přidruženého účtu Azure Storage. Zařízení vyžaduje úložiště token ze služby IoT Hub, která je specifická pro soubor, který chce zařízení nahrát. Zařízení využívá identifikátor URI SAS pro nahrání souboru do úložiště, a po dokončení nahrávání zařízení odešle oznámení o dokončení do služby IoT Hub. IoT Hub zkontroluje samotné nahrávání souborů je a pak přidá zprávu s oznámením nahrání souboru do koncového bodu služby připojeného souboru oznámení.

Nahrát soubor do služby IoT Hub ze zařízení, musíte nakonfigurovat centrem podle [přidružení služby Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) účtu k němu.

Zařízení lze následně [inicializovat nahrání](iot-hub-devguide-file-upload.md#initialize-a-file-upload) a pak [upozornění služby IoT hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) až se nahrávání dokončí. Volitelně, když se zařízení upozornění služby IoT Hub, že se nahrávání dokončí, službu můžete generovat [oznámení](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Kdy je použít

Nahrávání souborů použijte k odesílání mediálních souborů a velkých telemetrie dávky odeslaný přerušovaně připojeným zařízením nebo Komprimovat pro snížení šířky pásma.

Odkazovat na [pokyny komunikace typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) Pokud máte pochybnosti mezi pomocí ohlášených vlastností zprávy typu zařízení cloud a nahrání souboru.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Přidružení účtu služby Azure Storage pomocí služby IoT Hub

Pokud chcete používat funkce odesílání souborů, je třeba nejprve propojit účet služby Azure Storage do služby IoT Hub. Dokončení této úlohy, a to buď prostřednictvím webu Azure portal nebo prostřednictvím kódu programu [poskytovatele prostředků služby IoT Hub rozhraní REST API](/rest/api/iothub/iothubresource). Jakmile se službou IoT Hub, které jste přidružili účtu služby Azure Storage, tato služba vrátí identifikátor URI SAS zařízení při spuštění zařízení žádost o nahrání souboru.

[Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub](iot-hub-csharp-csharp-file-upload.md) návody poskytují kompletní postup procesu nahrávání souboru. Tyto příručky s postupy ukazují, jak pomocí webu Azure portal můžete přidružit účet úložiště služby IoT hub.

> [!NOTE]
> [Sad SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky zpracovává načítání identifikátor URI SAS, nahrání souboru a oznamování IoT Hub dokončená nahrávání.

## <a name="initialize-a-file-upload"></a>Inicializace odesílání souborů
IoT Hub má koncový bod speciálně pro zařízení, která žádost o identifikátor URI SAS pro úložiště pro nahrání souboru. Pokud chcete spustit proces nahrávání souborů, zařízení se odešle požadavek POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files` spolu s následujícím textem JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub vrací následující údaje, které zařízení použije k nahrání souboru:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Zastaralé: Inicializace odesílání souborů s GET

> [!NOTE]
> Tato část popisuje, jak získat identifikátor URI SAS ze služby IoT Hub zastaralé funkce. Použijte metodu POST je popsáno výše.

IoT Hub má dva koncové body REST pro podporu nahrávání souborů, z nich se má získat identifikátor URI SAS pro úložiště a druhou Oznámit dokončení odeslání služby IoT hub. Zařízení zahájí proces nahrávání souboru odesláním GET do služby IoT hub na `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Vrátí služby IoT hub:

* Identifikátor URI SAS specifické pro soubor k nahrání.

* ID korelace, který se má použít, až se nahrávání dokončí.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Oznámení služby IoT Hub z dokončené nahrávání

Zařízení odešle soubor do úložiště pomocí sad Azure Storage SDK. Po dokončení nahrávání se zařízení odešle požadavek POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` spolu s následujícím textem JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Hodnota `isSuccess` je logická hodnota, která určuje, zda soubor byl úspěšně odeslán. Stavový kód pro `statusCode` je stav pro nahrání souboru do úložiště a `statusDescription` odpovídá `statusCode`.

## <a name="reference-topics"></a>Referenční témata:

V následujících tématech vám poskytnout další informace o nahrávání souborů ze zařízení.

## <a name="file-upload-notifications"></a>Oznámení o nahrávání souborů

Volitelně když se zařízení upozornění služby IoT Hub, že se nahrávání dokončí, služby IoT Hub generuje zprávu s oznámením. Tato zpráva obsahuje název a úložiště umístění souboru.

Jak je vysvětleno v [koncové body](iot-hub-devguide-endpoints.md), IoT Hub doručí oznámení o nahrávání souborů prostřednictvím koncového bodu připojeného k služby (**/messages/servicebound/fileuploadnotifications**) jako zprávy. Sémantika příjmu pro oznámení o nahrávání souborů je stejný jako u zprávy typu cloud zařízení a mají stejné [životní cyklus zpráv](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Každou zprávu načíst z koncového bodu oznámení nahrávání souborů je záznam JSON s následujícími vlastnostmi:

| Vlastnost | Popis |
| --- | --- |
| EnqueuedTimeUtc |Časové razítko určující, kdy byla vytvořena oznámení. |
| DeviceId |**ID zařízení** zařízení, která nahrání souboru. |
| BlobUri |Identifikátor URI uloženého souboru. |
| BlobName |Název uloženého souboru. |
| LastUpdatedTime |Časové razítko označující poslední aktualizace souboru. |
| BlobSizeInBytes |Velikost uloženého souboru. |

**Příklad**. Tento příklad ukazuje obsah souboru odeslat zprávu oznámení.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Možnosti konfigurace oznámení nahrání souboru

Každé centrum IoT má následující možnosti konfigurace pro soubor, odeslat oznámení:

| Vlastnost | Popis | Rozsah a výchozí |
| --- | --- | --- |
| **enableFileUploadNotifications** |Určuje, zda oznámení o nahrávání souborů se zapisují do koncového bodu oznámení souboru. |Logická hodnota. Výchozí: True. |
| **fileNotifications.ttlAsIso8601** |Výchozí hodnota TTL pro oznámení o nahrávání souborů. |ISO_8601 interval až 48 H (minimální 1 minuta). Výchozí hodnota: 1 hodina. |
| **fileNotifications.lockDuration** |Doba trvání uzamknutí pro frontu oznámení nahrávání souboru. |5 do 300 sekund (minimální 5 sekund). Výchozí: 60 sekund. |
| **fileNotifications.maxDeliveryCount** |Maximální počet doručení pro tento soubor nahrajte frontě oznámení. |1 až 100. Výchozí: 100. |

## <a name="additional-reference-material"></a>Další referenční materiál

Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md) popisuje různé koncové body centra IoT pro operace za běhu a správy.

* [Omezování a kvótách](iot-hub-devguide-quotas-throttling.md) popisuje kvóty a omezování chování, které se vztahují ke službě IoT Hub.

* [Azure IoT zařízení a služby sady SDK](iot-hub-devguide-sdks.md) uvádí různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.

* [Dotazovací jazyk služby IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, slouží k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokolu MQTT.

## <a name="next-steps"></a>Další postup

Nyní jste zjistili, jak k nahrání souborů ze zařízení pomocí služby IoT Hub, vás může zajímat v následujících tématech příručky pro vývojáře IoT Hub:

* [Správa identit zařízení ve službě IoT Hub](iot-hub-devguide-identity-registry.md)

* [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

* [Použití dvojčat zařízení k synchronizaci stavu a konfigurace](iot-hub-devguide-device-twins.md)

* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Vyzkoušet si některé koncepty popsané v tomto článku, najdete v následujícím kurzu služby IoT Hub:

* [Postup nahrání souborů ze zařízení do cloudu pomocí služby IoT Hub](iot-hub-csharp-csharp-file-upload.md)