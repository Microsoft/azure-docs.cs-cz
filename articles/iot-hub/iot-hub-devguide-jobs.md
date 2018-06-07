---
title: Pochopení úlohy Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - plánování úloh spouštět na několika zařízeních připojení do služby IoT hub. Úlohy můžete aktualizovat značky a požadované vlastnosti a volat přímé metody na několika zařízeních.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 35b8536b944df39d0d47bf3529698fc94e51110e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633940"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Plánování úloh na několika zařízeních

Azure IoT Hub umožňuje počet stavební bloky jako [zařízení twin vlastnosti a značky] [ lnk-twin-devguide] a [přímé metody][lnk-dev-methods].  Back-end aplikace obvykle povolit operátory a Správci zařízení k aktualizaci a komunikovat se zařízeními IoT hromadně a v naplánovaném čase.  Úlohy provést aktualizace twin zařízení a přímé metody oproti sadě zařízení v naplánovaném čase.  Operátor byste například použili back-end aplikace, který iniciuje a sleduje úlohu restartovat sadu zařízení při vytváření 43 a podlaží 3 v čase, který nebude rušivý pro operace vytvoření.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zvažte použití úlohy když potřebujete plánovat a sledovat průběh některý z následujících aktivit na skupiny zařízení:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání metody přímé

## <a name="job-lifecycle"></a>Životní cyklus úlohy
Úlohy jsou iniciovaná back-end řešení a udržované pomocí služby IoT Hub.  Můžete spustit úlohu pomocí identifikátoru URI služby přístupem (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) a dotaz na průběh na úlohu prováděné prostřednictvím identifikátoru URI služby přístupem (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). K aktualizaci stavu spuštěných úloh, jakmile se zahájí úlohu, spusťte dotaz úlohy.

> [!NOTE]
> Při zahájení úlohy, názvů a hodnot vlastností mohou obsahovat pouze US-ASCII tisknutelná alfanumerické znaky, s výjimkou těch následující sady: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Úlohy provést přímý metody
Následující fragment kódu ukazuje podrobnosti požadavku HTTPS 1.1 pro provádění [přímá metoda] [ lnk-dev-methods] na sadu zařízení pomocí úlohy:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

Podmínka dotazu může být také na ID jednoho zařízení nebo na seznam zařízení, ID, jak je znázorněno v následujících příkladech:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT Hub dotazovací jazyk] [ lnk-query] obsahuje dotazovací jazyk IoT Hub v další podrobnosti.

## <a name="jobs-to-update-device-twin-properties"></a>Úlohy aktualizace twin vlastnosti zařízení
Následující fragment kódu ukazuje podrobnosti požadavku HTTPS 1.1 pro aktualizace zařízení dvojici vlastností pomocí úlohy:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>Dotaz na průběh na úlohách
Následující fragment kódu ukazuje podrobnosti požadavku HTTPS 1.1 pro [dotazování pro úlohy][lnk-query]:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

Je k dispozici continuationToken z odpovědi.  

## <a name="jobs-properties"></a>Vlastnosti úlohy
Následující seznam obsahuje vlastnosti a odpovídající popisy, které se dají použít při dotazování pro úlohy nebo výsledky úlohy.

| Vlastnost | Popis |
| --- | --- |
| **JobId** |Aplikace zadat ID pro úlohu. |
| **startTime** |Aplikace zadat čas spuštění (ISO-8601) pro úlohu. |
| **endTime** |Centrum IoT k dispozici po dokončení úlohy datum (ISO-8601). Platné jenom po úlohu dosáhne stavu 'dokončení'. |
| **type** |Typy úloh: |
| | **scheduledUpdateTwin**: úlohu použít k aktualizaci sadu požadované vlastnosti a značky. |
| | **scheduledDeviceMethod**: úloha používá k volání metody zařízení na sadu dvojčata zařízení. |
| **Stav** |Aktuální stav úlohy. Možné hodnoty pro stav: |
| | **Čekající**: naplánováno a čeká se na být zachyceny pomocí služby úlohy. |
| | **naplánované**: naplánováno na dobu v budoucnu. |
| | **spuštění**: aktuálně aktivní úlohy. |
| | **došlo ke zrušení**: Úloha byla zrušena. |
| | **se nezdařilo**: Nepodařilo se provést úlohu. |
| | **Dokončit**: Úloha byla dokončena. |
| **deviceJobStatistics** |Statistika týkající se spouštění úlohy. |
| | **deviceJobStatistics** vlastnosti: |
| | **deviceJobStatistics.deviceCount**: počet zařízení pro úlohu. |
| | **deviceJobStatistics.failedCount**: počet zařízení, kde úloha se nezdařila. |
| | **deviceJobStatistics.succeededCount**: počet zařízení, kde byla úloha úspěšná. |
| | **deviceJobStatistics.runningCount**: počet zařízení, které jsou aktuálně spuštěné úlohy. |
| | **deviceJobStatistics.pendingCount**: počet zařízení, které čekají na vyřízení. Pokud chcete spustit úlohu. |

### <a name="additional-reference-material"></a>Odkaz na další materiály
Další témata referenční příručka vývojáře IoT Hub patří:

* [Koncové body centra IoT] [ lnk-endpoints] popisuje různé koncových bodů, které každý IoT hub zpřístupní pro spuštění a management operace.
* [Omezování a kvóty] [ lnk-quotas] popisuje kvóty, které platí pro službu IoT Hub a omezení chování se očekává při použití služby.
* [Azure IoT zařízení a služby sady SDK] [ lnk-sdks] uvádí různé jazykové sady SDK můžete použít při vývoji aplikace zařízení a služby, které interakci s centrem IoT.
* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv] [ lnk-query] popisuje dotazovací jazyk IoT Hub. Tento dotaz jazyk se použije k načtení informací o úlohách a dvojčata zařízení ze služby IoT Hub.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup
Můžete vyzkoušet na některé z konceptů popsaných v tomto článku, najdete v následujícím kurzu IoT Hub:

* [Plán a všesměrovým úlohy][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
