---
title: Principy nahrávání souborů v Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – pomocí funkce nahrávání souborů IoT Hub můžete spravovat nahrávání souborů ze zařízení do kontejneru objektů BLOB služby Azure Storage.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 3286b464051b8fea88d2797d4f82b20fe432b4b8
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019525"
---
# <a name="upload-files-with-iot-hub"></a>Nahrávání souborů s využitím služby IoT Hub

Jak je podrobně popsáno v článku [IoT Hub Endpoints](iot-hub-devguide-endpoints.md) , může zařízení spustit nahrávání souborů odesláním oznámení prostřednictvím koncového bodu s přístupem k zařízení (**/Devices/{deviceId}/Files**). Když zařízení IoT Hub upozorní, že se nahrávání dokončí, IoT Hub pošle zprávu s oznámením o nahrání souboru prostřednictvím koncového bodu s přístupem ke službě **/Messages/servicebound/filenotifications** .

Místo toho, aby se zprávy od sebe IoT Hub, IoT Hub místo toho slouží jako dispečer k přidruženému účtu Azure Storage. Zařízení požaduje token úložiště od IoT Hub, které je specifické pro soubor, který si chce zařízení nahrát. Zařízení používá identifikátor URI SAS k nahrání souboru do úložiště a po dokončení nahrávání pošle zařízení oznámení o dokončení IoT Hub. IoT Hub kontroluje nahrávání souboru je dokončené a potom přidá zprávu oznámení o nahrání souboru do koncového bodu oznámení k souboru pro službu.

Před nahráním souboru, který IoT Hub ze zařízení, je nutné nakonfigurovat své centrum tak, že k němu [přidružíte Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) účet.

Vaše zařízení pak může [inicializovat nahrávání](iot-hub-devguide-file-upload.md#initialize-a-file-upload) a pak [informovat IoT Hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) po dokončení nahrávání. Pokud je v případě, že zařízení upozorňuje IoT Hub, že se nahrávání dokončí, může služba vygenerovat [zprávu s oznámením](iot-hub-devguide-file-upload.md#file-upload-notifications).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Kdy je použít

Pomocí nahrávání souborů můžete odesílat mediální soubory a velké dávky telemetrie, které jsou nahrané přerušovaně připojenými zařízeními nebo zkomprimované, aby se ušetřila šířka pásma.

V tématu doprovodné materiály týkající se [komunikace mezi zařízeními a cloudem](iot-hub-devguide-d2c-guidance.md) můžete v případě pochybnosti mezi použitím hlášených vlastností, zpráv ze zařízení do cloudu nebo nahráváním souborů.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Přidružte účet Azure Storage k IoT Hub

Chcete-li použít funkci nahrávání souborů, musíte nejprve propojit účet Azure Storage s IoT Hub. Tuto úlohu můžete dokončit buď pomocí Azure Portal, nebo programově prostřednictvím [rozhraní API REST pro IoT Hub poskytovatele prostředků](/rest/api/iothub/iothubresource). Po přidružení účtu Azure Storage k vašemu IoT Hub služba vrátí identifikátor URI SAS zařízení, když zařízení spustí požadavek na nahrání souboru.

[Nahrajte soubory ze zařízení do cloudu s IoT Hub](iot-hub-csharp-csharp-file-upload.md) průvodci postupy, které poskytují kompletní návod k procesu nahrání souboru. Tyto návody vám ukážou, jak pomocí Azure Portal přidružit účet úložiště ke službě IoT Hub.

> [!NOTE]
> Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky zpracovávají načítání identifikátorů URI SAS, nahrávají soubor a oznamují IoT Hub dokončeného nahrávání.

## <a name="initialize-a-file-upload"></a>Inicializace nahrávání souboru
IoT Hub má koncový bod specificky pro zařízení, aby si vyžádal identifikátor URI SAS pro úložiště pro nahrání souboru. Chcete-li spustit proces nahrání souboru, zařízení odešle požadavek POST `{iot hub}.azure-devices.net/devices/{deviceId}/files` s následujícím textem JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub vrátí následující data, která zařízení používají k nahrání souboru:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Zastaralé: inicializace nahrávání souboru pomocí GET

> [!NOTE]
> Tato část popisuje nepoužívané funkce pro příjem identifikátoru URI SAS z IoT Hub. Použijte metodu POST popsanou výše.

IoT Hub má dva koncové body REST pro podporu nahrávání souborů, jednu pro získání identifikátoru URI SAS pro úložiště a druhý pro oznamování služby IoT Hub dokončeného nahrávání. Zařízení spustí proces nahrání souboru odesláním GET do centra IoT na adrese `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . Centrum IoT vrátí:

* Identifikátor URI SAS specifický pro soubor, který se má nahrát.

* ID korelace, které se má použít, až se nahrávání dokončí.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Oznamovat IoT Hub dokončeného nahrávání souboru

Zařízení nahraje soubor do úložiště pomocí sad Azure Storage SDK. Po dokončení nahrávání odešle zařízení požadavek POST `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` s následujícím textem JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Hodnota `isSuccess` je logická hodnota, která označuje, zda byl soubor úspěšně nahrán. Stavový kód pro `statusCode` je stav odeslání souboru do úložiště a `statusDescription` odpovídá `statusCode` .

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata obsahují další informace o nahrávání souborů ze zařízení.

## <a name="file-upload-notifications"></a>Oznámení o nahrání souborů

Pokud je v případě, že zařízení IoT Hub upozorní na dokončení nahrávání, IoT Hub vygeneruje zprávu s oznámením. Tato zpráva obsahuje název a umístění úložiště souboru.

Jak je vysvětleno v [koncových bodech](iot-hub-devguide-endpoints.md), IoT Hub ukládá oznámení o nahrávání souborů prostřednictvím koncového bodu služby (**/Messages/servicebound/fileuploadnotifications**) jako zpráv. Sémantika příjmu pro oznámení o nahrávání souborů je stejná jako u zpráv z cloudu na zařízení a má stejný [životní cyklus zpráv](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Každá zpráva načtená z koncového bodu oznámení o nahrání souboru je záznam JSON s následujícími vlastnostmi:

| Vlastnost | Popis |
| --- | --- |
| EnqueuedTimeUtc |Časové razítko označující, kdy bylo oznámení vytvořeno. |
| DeviceId |**DeviceID** zařízení, které soubor nahrálo. |
| BlobUri |Identifikátor URI nahraného souboru |
| BlobName |Název nahraného souboru |
| LastUpdatedTime |Časové razítko, které indikuje, kdy se soubor naposledy aktualizoval |
| BlobSizeInBytes |Velikost nahraného souboru |

**Příklad**: Tento příklad ukazuje tělo zprávy s oznámením o nahrání souboru.

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

## <a name="file-upload-notification-configuration-options"></a>Možnosti konfigurace oznámení o nahrání souborů

Každé centrum IoT má následující možnosti konfigurace pro zasílání oznámení o nahrávání souborů:

| Vlastnost | Popis | Rozsah a výchozí |
| --- | --- | --- |
| **enableFileUploadNotifications** |Určuje, zda jsou do koncového bodu zasílání oznámení souborů zapsána oznámení o nahrání souborů. |Logick. Výchozí hodnota: true. |
| **Oznámení. ttlAsIso8601** |Výchozí hodnota TTL pro oznámení o nahrávání souborů |Interval ISO_8601 48H (minimálně 1 minutu). Výchozí hodnota: 1 hodina. |
| **Oznámení. lockDuration** |Doba trvání zámku pro frontu oznámení o nahrání souborů |5 až 300 sekund (minimálně 5 sekund). Výchozí hodnota: 60 sekund. |
| **Oznámení. maxDeliveryCount** |Maximální počet doručení pro frontu oznámení o nahrání souboru |1 až 100. Výchozí: 100. |

Tyto vlastnosti můžete nastavit ve službě IoT Hub pomocí Azure Portal, Azure CLI nebo PowerShellu. Informace o tom, jak se dozvíte, najdete v tématech v části [Konfigurace nahrávání souborů](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body služby IoT Hub pro operace run-time a Management.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisují chování kvót a omezení, která se vztahují na službu IoT Hub.

* Sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahují různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* Dotazovací jazyk [IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z IoT Hub o nečinnosti zařízení a úlohách.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak nahrávat soubory ze zařízení pomocí IoT Hub, možná vás zajímá následující témata IoT Hub příručka pro vývojáře:

* [Správa identit zařízení v IoT Hub](iot-hub-devguide-identity-registry.md)

* [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

* [Pro synchronizaci stavu a konfigurací použít vlákna zařízení](iot-hub-devguide-device-twins.md)

* [Vyvolání přímé metody v zařízení](iot-hub-devguide-direct-methods.md)

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud si chcete vyzkoušet některé z konceptů popsaných v tomto článku, přečtěte si následující IoT Hub kurz:

* [Postup nahrání souborů ze zařízení do cloudu pomocí IoT Hub](iot-hub-csharp-csharp-file-upload.md)