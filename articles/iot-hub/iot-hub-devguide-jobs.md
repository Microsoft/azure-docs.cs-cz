---
title: Principy úloh Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – plánování úloh pro spuštění na více zařízeních připojených k vašemu centru IoT hub. Úlohy můžete aktualizovat značky a požadované vlastnosti a vyvolat přímé metody na více zařízeních.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730109"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Plánování úloh na několika zařízeních

Azure IoT Hub umožňuje řadu stavebních bloků, jako jsou [vlastnosti dvojčete zařízení a značky](iot-hub-devguide-device-twins.md) a [přímé metody](iot-hub-devguide-direct-methods.md). Back-endové aplikace obvykle umožňují správcům a operátorům zařízení aktualizovat zařízení IoT a pracovat s nimi hromadně a v naplánovaný čas. Úlohy spouštějí aktualizace dvojčete zařízení a přímé metody proti sadě zařízení v naplánovaném čase. Operátor by například použít back-end aplikace, která iniciuje a sleduje úlohu restartovat sadu zařízení v budově 43 a podlaží 3 v době, která by neměla rušivá provoz budovy.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zvažte použití úloh, když potřebujete naplánovat a sledovat průběh některé z následujících aktivit na sadě zařízení:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolat přímé metody

## <a name="job-lifecycle"></a>Životní cyklus práce

Úlohy jsou iniciovány back-endem řešení a spravovány ioT Hubem. Úlohu můžete zahájit prostřednictvím identifikátoru URI orientovaného na službu (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) a`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`dotazovat se na průběh provádění úlohy prostřednictvím identifikátoru URI orientovaného na službu ( ). Chcete-li aktualizovat stav spuštěných úloh po zahájení úlohy, spusťte dotaz na úlohu.

> [!NOTE]
> Při zahájení úlohy mohou názvy vlastností a hodnoty obsahovat pouze tisknutelné alfanumerické funkce US-ASCII, s výjimkou všech v následující sadě:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Úlohy pro provádění přímých metod

Následující úryvek zobrazuje podrobnosti požadavku HTTPS 1.1 pro provádění [přímé metody](iot-hub-devguide-direct-methods.md) na sadě zařízení používajících úlohu:

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

Podmínka dotazu může být také na jednom ID zařízení nebo v seznamu ID zařízení, jak je znázorněno v následujících příkladech:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Dotazovací jazyk ioT hubu](iot-hub-devguide-query-language.md) zahrnuje dotazovací jazyk ioT hubu v dalších podrobnostech.

Následující úryvek zobrazuje požadavek a odpověď pro úlohu naplánovanou na volání přímé metody s názvem testMethod na všech zařízeních v contoso-hub-1:

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

## <a name="jobs-to-update-device-twin-properties"></a>Úlohy pro aktualizaci vlastností dvojčete zařízení

Následující úryvek zobrazuje podrobnosti požadavku HTTPS 1.1 pro aktualizaci vlastností dvojčete zařízení pomocí úlohy:

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
> Vlastnost *updateTwin* vyžaduje platnou shodu etag; například `etag="*"`.

Následující úryvek zobrazuje požadavek a odpověď pro úlohu naplánovanou na aktualizaci vlastností dvojčete zařízení pro testovací zařízení v contoso-hub-1:

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

## <a name="querying-for-progress-on-jobs"></a>Dotazování na pokrok v úlohách

Následující úryvek zobrazuje podrobnosti požadavku HTTPS 1.1 pro dotazování na úlohy:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken je k dispozici z odpovědi.

Můžete dotaz na stav spuštění úlohy na každém zařízení pomocí [dotazovacího jazyka Služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Vlastnosti úloh

V následujícím seznamu jsou uvedeny vlastnosti a odpovídající popisy, které lze použít při dotazování na úlohy nebo výsledky úlohy.

| Vlastnost | Popis |
| --- | --- |
| **jobId** |Aplikace za předpokladu, ID pro práci. |
| **startTime** |Aplikace poskytla čas zahájení (ISO-8601) pro úlohu. |
| **endTime** |IoT Hub za předpokladu, datum (ISO-8601) po dokončení úlohy. Platí pouze poté, co úloha dosáhne stavu dokončeno. |
| **Typ** |Typy pracovních míst: |
| | **scheduleUpdateTwin**: Úloha použitá k aktualizaci sady požadovaných vlastností nebo značek. |
| | **scheduleDeviceMethod**: Úloha použitá k vyvolání metody zařízení na sadě dvojčat zařízení. |
| **Stav** |Aktuální stav úlohy. Možné hodnoty stavu: |
| | **Čeká na vyřízení**: Naplánováno a čeká na vyzvednutí službou práce. |
| | **naplánováno**: Naplánováno na čas v budoucnu. |
| | **běh**: Aktuálně aktivní úloha. |
| | **zrušeno**: Úloha byla zrušena. |
| | **Nezdařilo se**: Úloha se nezdařila. |
| | **dokončeno**: Úloha byla dokončena. |
| **deviceJobStatistics** |Statistiky o provádění úlohy. |
| | **vlastnosti deviceJobStatistics:** |
| | **deviceJobStatistics.deviceCount**: Počet zařízení v úloze. |
| | **deviceJobStatistics.failedCount**: Počet zařízení, kde se úloha nezdařila. |
| | **deviceJobStatistics.succeededCount**: Počet zařízení, kde byla úloha úspěšná. |
| | **deviceJobStatistics.runningCount**: Počet zařízení, která aktuálně spouštějí úlohu. |
| | **deviceJobStatistics.pendingCount**: Počet zařízení, která čekají na spuštění úlohy. |

### <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* [Koncové body ioT hubu](iot-hub-devguide-endpoints.md) popisují různé koncové body, které každý ioT hub zveřejňuje pro operace za běhu a správy.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisuje kvóty, které platí pro službu IoT Hub a omezení chování očekávat při použití služby.

* [Sady SDK zařízení a služeb Azure IoT](iot-hub-devguide-sdks.md) uvádí různé sady SDK, které můžete použít při vývoji aplikací pro zařízení i služeb, které interagují s službou IoT Hub.

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk služby IoT Hub. Tento dotazovací jazyk slouží k načtení informací z centra IoT Hub o dvojčatech a úlohách vašeho zařízení.

* [Podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet některé koncepty popsané v tomto článku, podívejte se na následující kurz centra IoT Hub:

* [Úlohy vysílání a plánování](iot-hub-node-node-schedule-jobs.md)