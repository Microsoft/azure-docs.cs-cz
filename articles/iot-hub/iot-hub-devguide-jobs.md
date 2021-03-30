---
title: Vysvětlení úloh služby Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – plánování úloh, které se spouštějí na více zařízeních připojených ke službě IoT Hub. Úlohy mohou aktualizovat značky a požadované vlastnosti a vyvolat přímé metody na více zařízení.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730109"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Plánování úloh na několika zařízeních

Azure IoT Hub umožňuje řadu stavebních bloků, jako jsou [vlastnosti a značky](iot-hub-devguide-device-twins.md) , které jsou v zařízení a [přímé metody](iot-hub-devguide-direct-methods.md). Back-endové aplikace obvykle umožňují správcům a operátorům zařízení aktualizovat a komunikovat se zařízeními IoT hromadně a v naplánovaném čase. Úlohy spouštějí v naplánovaném čase nedokončené aktualizace zařízení a přímé metody pro sadu zařízení. Operátor by například používal back-end aplikaci, která iniciuje a sleduje úlohu pro restartování sady zařízení v sestavách sestavování 43 a patra 3 v době, která by nenarušila operace sestavení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zvažte použití úloh, pokud potřebujete naplánovat a sledovat průběh libovolné z následujících aktivit na sadě zařízení:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolání přímých metod

## <a name="job-lifecycle"></a>Životní cyklus úlohy

Úlohy jsou iniciovány back-end řešení a udržovány IoT Hub. Úlohu můžete iniciovat pomocí identifikátoru URI s přístupem ke službě ( `PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30` ) a dotazovat se na průběh provádění úlohy prostřednictvím identifikátoru URI s přístupem k službě ( `GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30` ). Chcete-li aktualizovat stav spuštěných úloh po zahájení úlohy, spusťte dotaz na úlohu.

> [!NOTE]
> Když zahájíte úlohu, názvy vlastností a hodnoty můžou obsahovat jenom tisknutelné alfanumerické znaky US-ASCII, s výjimkou jakékoli v následující sadě: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Úlohy ke spuštění přímých metod

Následující fragment kódu ukazuje Podrobnosti žádosti HTTPS 1,1 pro spuštění [přímé metody](iot-hub-devguide-direct-methods.md) na sadě zařízení pomocí úlohy:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Podmínka dotazu může být také v jednom ID zařízení nebo v seznamu ID zařízení, jak je znázorněno v následujících příkladech:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub dotazovací jazyk](iot-hub-devguide-query-language.md) obsahuje další podrobnosti IoT Hub dotazovací jazyk.

Následující fragment kódu ukazuje požadavek a odpověď úlohy naplánované na volání přímé metody s názvem testMethod na všech zařízeních v contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Úlohy, které aktualizují vlastnosti vlastností zařízení

Následující fragment kódu ukazuje Podrobnosti žádosti HTTPS 1,1 pro aktualizaci vlastností, které jsou v zařízení, pomocí úlohy:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Vlastnost *updateTwin* vyžaduje platnou shodu značky ETag; například `etag="*"` .

Následující fragment kódu ukazuje požadavek a odpověď na úlohu naplánovanou na aktualizaci vlastností zařízení, které jsou pro test-Device na rozbočovači contoso-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Dotazování na průběh úloh

Následující fragment kódu ukazuje Podrobnosti žádosti HTTPS 1,1 pro dotazování na úlohy:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

Token continuationtoken je poskytována z odpovědi.

Na každé zařízení můžete zadat dotaz na stav spuštění úlohy pomocí [IoT Hubho dotazovacího jazyka pro vlákna, úlohy a směrování zpráv v zařízeních](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Vlastnosti úloh

V následujícím seznamu jsou uvedeny vlastnosti a odpovídající popisy, které lze použít při dotazování na úlohy nebo výsledky úloh.

| Vlastnost | Popis |
| --- | --- |
| **Úlohy** |ID poskytnuté aplikace pro úlohu. |
| **Spuštění** |Aplikace zadala počáteční čas (ISO-8601) pro úlohu. |
| **endTime** |IoT Hub poskytnuté datum (ISO-8601) pro dokončení úlohy. Platí až po dosažení stavu dokončeno. |
| **textový** |Typy úloh: |
| | **scheduleUpdateTwin**: úloha používaná k aktualizaci sady požadovaných vlastností nebo značek. |
| | **scheduleDeviceMethod**: úloha, která se používá k vyvolání metody zařízení u sady vláken zařízení. |
| **stav** |Aktuální stav úlohy. Možné hodnoty pro stav: |
| | **čeká na vyřízení**: naplánované a čekající na vyzvednutí službou úloh. |
| | **plánováno**: naplánováno na čas v budoucnosti. |
| | **spuštěno**: aktuálně aktivní úloha. |
| | **zrušeno**: úloha byla zrušena. |
| | **nepovedlo se**: Úloha selhala. |
| | **dokončeno**: úloha byla dokončena. |
| **deviceJobStatistics** |Statistika provádění úlohy |
| | **deviceJobStatistics** vlastnosti: |
| | **deviceJobStatistics. počet zařízení**: počet zařízení v úloze. |
| | **deviceJobStatistics. failedCount**: počet zařízení, ve kterých se úloha nezdařila. |
| | **deviceJobStatistics. succeededCount**: počet zařízení, do kterých byla úloha úspěšně dokončena. |
| | **deviceJobStatistics. runningCount**: počet zařízení, na kterých je aktuálně spuštěna úloha. |
| | **deviceJobStatistics. pendingCount**: počet zařízení, která čekají na spuštění úlohy. |

### <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace run-time a Management.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisují kvóty, které se vztahují na službu IoT Hub a omezení chování, které se má při používání služby očekávat.

* Sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahují různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* [IoT Hub dotazovací jazyk pro vlákna zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk IoT Hub. Pomocí tohoto dotazovacího jazyka můžete načítat informace z IoT Hub o nečinnosti zařízení a úlohách.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet některé z konceptů popsaných v tomto článku, přečtěte si následující IoT Hub kurz:

* [Úlohy vysílání a plánování](iot-hub-node-node-schedule-jobs.md)