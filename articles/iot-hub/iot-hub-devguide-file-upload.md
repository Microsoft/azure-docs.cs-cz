---
title: Principy nahrávání souborů služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pomocí funkce nahrávání souborů služby IoT Hub můžete spravovat nahrávání souborů ze zařízení do kontejneru objektů blob úložiště Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom: mqtt
ms.openlocfilehash: 35337a99706f25d62964e08a5b16cd8e81f315c6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730300"
---
# <a name="upload-files-with-iot-hub"></a>Nahrávání souborů s využitím služby IoT Hub

Jak je podrobně popsáno v článku [koncových bodů ioT hubu,](iot-hub-devguide-endpoints.md) zařízení může spustit nahrávání souboru odesláním oznámení prostřednictvím koncového bodu směřujícího k zařízení (**/devices/{deviceId}/files**). Když zařízení upozorní službu IoT Hub, že nahrávání je dokončeno, služba IoT Hub odešle oznámení o nahrání souboru prostřednictvím koncového bodu pro službu **/messages/servicebound/filenotifications.**

Místo zprostředkování zpráv prostřednictvím samotného ioT hubu funguje Služba IoT Hub místo toho jako dispečer přidruženého účtu azure storage. Zařízení požaduje token úložiště z služby IoT Hub, který je specifický pro soubor, který chce zařízení odeslat. Zařízení používá identifikátor URI SAS k nahrání souboru do úložiště a po dokončení nahrávání odešle zařízení oznámení o dokončení služby IoT Hub. IoT Hub zkontroluje, zda je nahrávání souboru dokončené, a pak přidá oznámení o nahrání souboru do koncového bodu oznámení o souboru směřujícím ke službě.

Před nahráním souboru do služby IoT Hub ze zařízení je nutné nakonfigurovat centrum tak, že k němu [přisunete účet Služby Azure Storage.](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)

Vaše zařízení pak může [inicializovat nahrávání](iot-hub-devguide-file-upload.md#initialize-a-file-upload) a po dokončení nahrávání [upozornit centrum IoT.](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) Volitelně když zařízení upozorní službu IoT Hub, že nahrávání je dokončeno, může služba vygenerovat [oznamovací zprávu](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Kdy je použít

Pomocí nahrávání souborů můžete odesílat mediální soubory a velké telemetrické dávky nahrané občas připojenými zařízeními nebo komprimované pro úsporu šířky pásma.

V případě pochybností mezi použitím ohlášených vlastností, zpráv mezi zařízeními a cloudem nebo nahráváním souborů se podívejte [na pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a cloudem.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Přidružení účtu úložiště Azure k centru IoT Hub

Pokud chcete použít funkci nahrávání souborů, musíte nejdřív propojit účet Úložiště Azure s centrem IoT Hub. Tento úkol můžete dokončit buď prostřednictvím portálu Azure, nebo programově prostřednictvím [rest API poskytovatele prostředků služby IoT Hub](/rest/api/iothub/iothubresource). Jakmile přidrumíte účet Služby Azure Storage k centru IoT Hub, služba vrátí identifikátor URI SAS do zařízení, když zařízení spustí požadavek na odeslání souboru.

[Nahrát soubory ze zařízení do cloudu pomocí](iot-hub-csharp-csharp-file-upload.md) návodů pro IoT Hub poskytují kompletní návod k procesu nahrávání souborů. Tyto postupy vám ukážou, jak pomocí portálu Azure přidružit účet úložiště k centru IoT.

> [!NOTE]
> Sady [Azure IoT SDK](iot-hub-devguide-sdks.md) automaticky zpracovávají načítání identifikátoru URI SAS, nahrávání souboru a upozornění služby IoT Hub na dokončené nahrávání.

## <a name="initialize-a-file-upload"></a>Inicializovat odeslání souboru
IoT Hub má koncový bod speciálně pro zařízení požádat O identifikátor URI SAS pro úložiště k nahrání souboru. Chcete-li spustit proces nahrávání souborů, zařízení `{iot hub}.azure-devices.net/devices/{deviceId}/files` odešle požadavek POST s následujícím tělem JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub vrátí následující data, která zařízení používá k nahrání souboru:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Zastaralé: inicializovat nahrávání souboru pomocí get

> [!NOTE]
> Tato část popisuje zastaralé funkce pro příjem identifikátoru URI SAS z centra IoT Hub. Použijte metodu POST popsanou dříve.

IoT Hub má dva koncové body REST pro podporu nahrávání souborů, jeden získat Identifikátor URI SAS pro úložiště a druhý upozornit centrum IoT na dokončené nahrávání. Zařízení spustí proces nahrávání souborů odesláním GET do centra `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`IoT hub na adrese . Centrum IoT vrátí:

* Identifikátor URI SAS specifický pro soubor, který má být odeslán.

* ID korelace, které se použije po dokončení nahrávání.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Upozornit ioT Hub na dokončené nahrání souboru

Zařízení nahraje soubor do úložiště pomocí sad Azure Storage SDKs. Po dokončení nahrávání zařízení odešle požadavek `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` POST s následujícím tělem JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Hodnota `isSuccess` je logická hodnota, která označuje, zda byl soubor úspěšně odeslán. Stavový kód `statusCode` pro je stav pro nahrání souboru `statusDescription` do úložiště `statusCode`a odpovídá .

## <a name="reference-topics"></a>Referenční témata:

Následující referenční témata poskytují další informace o nahrávání souborů ze zařízení.

## <a name="file-upload-notifications"></a>Oznámení o nahrání souborů

Volitelně když zařízení upozorní službu IoT Hub, že nahrávání je dokončeno, služba IoT Hub vygeneruje oznámení. Tato zpráva obsahuje název a umístění úložiště souboru.

Jak je vysvětleno v [Koncových bodech](iot-hub-devguide-endpoints.md), Služba IoT Hub doručuje oznámení o nahrávání souborů prostřednictvím koncového bodu směřujícího ke službě (**/messages/servicebound/fileuploadnotifications**) jako zprávy. Příjem sémantiku pro oznámení o nahrání souborů jsou stejné jako u zpráv z cloudu na zařízení a mají stejný [životní cyklus zpráv](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Každá zpráva načtená z koncového bodu oznámení o nahrání souboru je záznam JSON s následujícími vlastnostmi:

| Vlastnost | Popis |
| --- | --- |
| EnqueuedTimeUtc |Časové razítko označující, kdy bylo oznámení vytvořeno. |
| DeviceId |**DeviceId** zařízení, které soubor nahrálo. |
| Objekt BlobUri |Identifikátor URI nahraného souboru. |
| Název objektu BlobName |Název nahraného souboru. |
| LastUpdatedTime |Časové razítko označující, kdy byl soubor naposledy aktualizován. |
| Objekt blobSizeInbajtů |Velikost nahraného souboru. |

**Příklad**. Tento příklad ukazuje text oznámení o nahrání souboru.

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

Každé centrum IoT má následující možnosti konfigurace pro oznámení o nahrávání souborů:

| Vlastnost | Popis | Rozsah a výchozí |
| --- | --- | --- |
| **enableFileUploadNotifications** |Určuje, zda se oznámení o nahrání souborů zapisují do koncového bodu oznámení o souborech. |Bool. Výchozí: True. |
| **souborNotifications.ttlAsIso8601** |Výchozí TTL pro oznámení o nahrávání souborů. |ISO_8601 interval až 48H (minimálně 1 minuta). Výchozí hodnota: 1 hodina. |
| **fileNotifications.lockDuration** |Uzamkněte dobu trvání fronty oznámení o nahrání souboru. |5 až 300 sekund (minimálně 5 sekund). Výchozí: 60 sekund. |
| **fileNotifications.maxDeliveryCount** |Maximální počet doručení pro frontu oznámení o nahrání souboru. |1 až 100. Výchozí hodnota: 100. |

Tyto vlastnosti můžete nastavit ve svém centru IoT pomocí portálu Azure, Azure CLI nebo PowerShellu. Informace o tom, jak, naleznete v tématech v části [Konfigurovat nahrávání souborů](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* [Koncové body ioT hubu](iot-hub-devguide-endpoints.md) popisují různé koncové body centra IoT hub pro operace run-time a správy.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisuje kvóty a omezení chování, které platí pro službu IoT Hub.

* [Sady SDK zařízení a služeb Azure IoT](iot-hub-devguide-sdks.md) uvádí různé sady SDK, které můžete použít při vývoji aplikací pro zařízení i služeb, které interagují s službou IoT Hub.

* [Dotazovací jazyk IoT Hub](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk, který můžete použít k načtení informací z ioT hubu o dvojčatech a úlohách vašeho zařízení.

* [Podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Teď jste se naučili, jak nahrávat soubory ze zařízení pomocí služby IoT Hub, které vás mohou zajímat následující témata s průvodcem pro vývojáře služby IoT Hub:

* [Správa identit zařízení v centru IoT Hub](iot-hub-devguide-identity-registry.md)

* [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

* [Synchronizace stavu a konfigurací pomocí dvojčat zařízení](iot-hub-devguide-device-twins.md)

* [Vyvolání přímé metody na zařízení](iot-hub-devguide-direct-methods.md)

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Chcete-li vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurz centra IoT Hub:

* [Jak nahrát soubory ze zařízení do cloudu pomocí služby IoT Hub](iot-hub-csharp-csharp-file-upload.md)