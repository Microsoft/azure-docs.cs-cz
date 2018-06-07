---
title: Pochopení nahrávání souborů Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - použijte funkci nahrávání souboru IoT Hub pro správu nahrávání souborů ze zařízení do kontejner objektu blob úložiště Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: e16d32bdba1374540c03d1034a94192a54e6a109
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634892"
---
# <a name="upload-files-with-iot-hub"></a>Nahrání souborů s centrem IoT

Podle popisu v [koncové body centra IoT] [ lnk-endpoints] článku zařízení můžete iniciovat načtení souboru odesílání oznámení prostřednictvím koncového bodu směřujících zařízení (**/devices/ {deviceId} / soubory**). Když zařízení oznámí IoT Hub, nahrávaný je dokončena, IoT Hub odešle zprávu souboru odesílání oznámení prostřednictvím **/messages/servicebound/filenotifications** koncový bod služby přístupem.

Místo zprostředkovatelské zprávy prostřednictvím služby IoT Hub, samotné místo toho IoT Hub funguje jako dispečera do přidruženého účtu úložiště Azure. Zařízení požadavků úložiště token ze služby IoT Hub, která je specifická pro soubor, který zařízení, které chcete nahrát. Zařízení používá identifikátor URI SAS nahrát soubor do úložiště a po dokončení nahrávání zařízení odesílá do služby IoT Hub oznámení o dokončení. IoT Hub zkontroluje nahrávání souborů je a pak přidá soubor odesílání oznámení do koncového bodu služby směřujících soubor oznámení.

Nahrát soubor do služby IoT Hub ze zařízení, musíte nakonfigurovat pomocí vašeho centra [přidružení Azure Storage] [ lnk-associate-storage] účet k němu.

Pak můžete zařízení [inicializovat nahrávaný] [ lnk-initialize] a potom [upozornění služby IoT hub] [ lnk-notify] po dokončení nahrávání. Volitelně, když zařízení oznámí IoT Hub, nahrávání je dokončena, služba může generovat [oznámení][lnk-service-notification].

### <a name="when-to-use"></a>Kdy je použít

Nahrávání souborů použijte k odesílání souborů médií a velké telemetrie dávek odeslaný občas připojená zařízení nebo Komprimovat pro snížení šířky pásma.

Odkazovat na [pokyny komunikace zařízení cloud] [ lnk-d2c-guidance] Pokud máte pochybnosti mezi použitím hlášen vlastnostech, zpráv typu zařízení cloud nebo nahrávání souborů.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Přidružit účet úložiště Azure IoT Hub

Pokud chcete používat funkci nahrávání souboru, je nutné nejprve propojit účet úložiště Azure do služby IoT Hub. Můžete tuto úlohu dokončit buď prostřednictvím [portál Azure][lnk-management-portal], nebo programově pomocí [zprostředkovatele prostředků služby IoT Hub rozhraní REST API][lnk-resource-provider-apis]. Po přidružený účet úložiště Azure IoT Hub, službu vrátí identifikátor URI pro SAS na zařízení, pokud zařízení zahájí žádost o odeslání souboru.

> [!NOTE]
> [SDK služby Azure IoT] [ lnk-sdks] automaticky zpracování načítání identifikátor URI SAS, nahrávání souboru a oznamování IoT Hub dokončená nahrávání.


## <a name="initialize-a-file-upload"></a>Inicializace nahrávání souborů
IoT Hub má koncový bod speciálně pro zařízení do požadavku URI SAS pro úložiště pro nahrání souboru. K zahájení procesu nahrávání souborů, zařízení, odešle požadavek POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files` spolu s následujícím textem JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub vrací následující data, která zařízení používá k odeslání souboru:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Zastaralé: Inicializace nahrání souboru s GET

> [!NOTE]
> Tato část popisuje, jak získat identifikátor URI SAS ze služby IoT Hub zastaralé funkce. Použijte metodu POST popsané.

IoT Hub má dva koncové body REST pro podporu nahrávání souborů, jednu pro získat identifikátor URI SAS pro úložiště a další služby IoT hub dokončené odeslání oznámení. Zařízení iniciuje procesu nahrávání souboru zasláním GET do služby IoT hub v `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Služba IoT hub vrací:

* URI specifické pro soubor SAS k odeslání.
* ID korelace, který se má použít po dokončení nahrávání.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Oznámit IoT Hub odeslání dokončené souboru

Zařízení je zodpovědná za nahrát soubor do úložiště pomocí sady SDK úložiště Azure. Po dokončení nahrávání zařízení odešle požadavek POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` spolu s následujícím textem JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Hodnota `isSuccess` je logická hodnota udávající, zda má soubor byl úspěšně odeslán. Stavový kód pro `statusCode` je stav pro nahrávání souborů do úložiště a `statusDescription` odpovídá `statusCode`.

## <a name="reference-topics"></a>Témata odkazů:

Následující referenční témata poskytují další informace o nahrávání souborů ze zařízení.

## <a name="file-upload-notifications"></a>Oznámení o odeslání souboru

Volitelně můžete při zařízení oznámí IoT Hub, nahrávaný je dokončena, IoT Hub vygeneruje zprávu oznámení, která obsahuje umístění a jeho název souboru.

Jak je popsáno v [koncové body][lnk-endpoints], IoT Hub zajišťuje oznámení o odeslání souboru prostřednictvím koncového bodu služby přístupem (**/messages/servicebound/fileuploadnotifications**) jako zprávy. Sémantika receive pro oznámení o odeslání souboru je stejný jako u zprávy typu cloud zařízení a mít stejný [životní cyklus zpráv][lnk-lifecycle]. Každou zprávu, získán z koncového bodu oznámení nahrávání souboru je záznam JSON s následujícími vlastnostmi:

| Vlastnost | Popis |
| --- | --- |
| EnqueuedTimeUtc |Časové razítko označující vytvoření oznámení. |
| DeviceId |**DeviceId** zařízení, která nahrát soubor. |
| BlobUri |Identifikátor URI nahrávaný soubor. |
| BlobName |Název uloženého souboru. |
| LastUpdatedTime |Časové razítko označující poslední aktualizace souboru. |
| BlobSizeInBytes |Velikost nahrávaný soubor. |

**Příklad**. Tento příklad ukazuje, text soubor odeslat zprávu oznámení.

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

## <a name="file-upload-notification-configuration-options"></a>Možnosti konfigurace oznámení nahrávání souborů

Každý IoT hub zpřístupní následující možnosti konfigurace pro oznámení o odeslání souboru:

| Vlastnost | Popis | Rozsah a výchozí |
| --- | --- | --- |
| **enableFileUploadNotifications** |Určuje, zda jsou oznámení o odeslání souboru zapisovány do koncového bodu oznámení souboru. |Logická hodnota. Výchozí: True. |
| **fileNotifications.ttlAsIso8601** |Výchozí hodnota TTL pro oznámení o odeslání souboru. |ISO_8601 interval až 48 H (minimální 1 minutu). Výchozí hodnota: 1 hodina. |
| **fileNotifications.lockDuration** |Doba trvání uzamčení pro frontu oznámení nahrávání souboru. |5 do 300 sekund (minimální 5 sekund). Výchozí: 60 sekund. |
| **fileNotifications.maxDeliveryCount** |Počet maximální doručení pro soubor nahrát fronta oznámení. |1 až 100. Výchozí: 100. |

## <a name="additional-reference-material"></a>Odkaz na další materiály

Další témata referenční příručka vývojáře IoT Hub patří:

* [Koncové body centra IoT] [ lnk-endpoints] popisuje různé koncových bodů, které každý IoT hub zpřístupní pro spuštění a management operace.
* [Omezování a kvóty] [ lnk-quotas] popisuje kvóty a omezení chování, které se vztahují ke službě IoT Hub.
* [Azure IoT zařízení a služby sady SDK] [ lnk-sdks] uvádí různé jazykové sady SDK můžete použít při vývoji aplikace zařízení a služby, které interakci s centrem IoT.
* [IoT Hub dotazovací jazyk] [ lnk-query] popisuje dotazovací jazyk, můžete použít k načtení informací ze služby IoT Hub o úlohách a dvojčata zařízení.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup

Nyní jste se naučili Postup nahrání souborů ze zařízení pomocí služby IoT Hub, může zajímat v následujících tématech Příručka vývojáře IoT Hub:

* [Správa identit zařízení IoT hub][lnk-devguide-identities]
* [Řízení přístupu ke službě IoT Hub][lnk-devguide-security]
* [Pomocí dvojčata zařízení synchronizovat stavu a konfigurace][lnk-devguide-device-twins]
* [Volání metody přímé na zařízení][lnk-devguide-directmethods]
* [Plánování úloh na několika zařízeních][lnk-devguide-jobs]

Můžete vyzkoušet na některé z konceptů popsaných v tomto článku, najdete v následujícím kurzu IoT Hub:

* [Postup nahrání souborů ze zařízení do cloudu s centrem IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
