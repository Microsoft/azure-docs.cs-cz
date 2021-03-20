---
title: Azure Stream Analytics na hraničních zařízeních IoT
description: Vytvářejte hraniční úlohy v Azure Stream Analytics a nasaďte je do zařízení s Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012610"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na hraničních zařízeních IoT
 
Azure Stream Analytics na IoT Edge vývojářům umožňuje nasadit v reálném čase analytickou logiku blíže k zařízením IoT, aby mohli odemknout celou hodnotu dat generovaných zařízením. Služba Azure Stream Analytics je určená k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. Podniky můžou nasazovat řídicí logiku blízko k průmyslovým operacím a doplňovat analýzy velkých objemů dat v cloudu.

Azure Stream Analytics na IoT Edge běží v rámci [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) architektury. Jakmile se úloha vytvoří v Stream Analytics, můžete ji nasadit a spravovat pomocí IoT Hub.

## <a name="common-scenarios"></a>Obvyklé scénáře

Tato část popisuje běžné scénáře Stream Analytics IoT Edge. Následující diagram znázorňuje tok dat mezi zařízeními IoT a cloudem Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagram vysoké úrovně IoT Edge":::

### <a name="low-latency-command-and-control"></a>Příkaz a ovládací prvek s nízkou latencí

Systémy zabezpečení výroby musí reagovat na provozní data s nízkou latencí. Díky Stream Analytics v IoT Edge můžete analyzovat data senzorů téměř v reálném čase a příkazy vydávat se při detekci anomálií za účelem zastavení počítače nebo spuštění výstrah.

### <a name="limited-connectivity-to-the-cloud"></a>Omezené připojení ke cloudu

Důležité systémy, jako je vzdálené řešení dolování, připojená plavidla nebo vrtné plavby, potřebují analyzovat a reagovat na data, i když je cloudové připojení přerušované. Díky Stream Analytics vaše logika streamování běží nezávisle na připojení k síti a vy můžete zvolit, co odesíláte do cloudu pro další zpracování nebo uložení.

### <a name="limited-bandwidth"></a>Omezená šířka pásma

Objem dat vytvářených stroji Jet nebo připojenými automobily může být tak velký, aby před odesláním do cloudu bylo nutné data filtrovat nebo předběžně zpracovat. Pomocí Stream Analytics můžete filtrovat nebo agregovat data, která je třeba odeslat do cloudu.

### <a name="compliance"></a>Dodržování předpisů

Dodržování legislativních předpisů může vyžadovat, aby některá data byla před odesláním do cloudu místně anonymní nebo agregovaná.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy v Azure Stream Analytics

Úlohy Stream Analytics Edge se spouštějí v kontejnerech nasazených do [Azure IoT Edge zařízení](../iot-edge/about-iot-edge.md). Hraniční úlohy se skládají ze dvou částí:

* Cloudová součást, která zodpovídá za definici úlohy: uživatelé definují vstupy, výstupy, dotazy a další nastavení, například události mimo pořadí v cloudu.

* Modul spuštěný ve vašich zařízeních IoT. Modul obsahuje modul Stream Analytics a přijímá definici úlohy z cloudu. 

Stream Analytics používá IoT Hub k nasazení hraničních úloh do zařízení. Další informace najdete v tématu [nasazení IoT Edge](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Úloha Azure Stream Analytics Edge":::

## <a name="edge-job-limitations"></a>Omezení úlohy Edge

Cílem je mít paritu mezi IoT Edge úlohami a cloudovým úlohami. Většina funkcí jazyka SQL pro dotazy je podporována pro Edge i Cloud. Pro úlohy Edge se ale nepodporují následující funkce:
* Uživatelsky definované funkce (UDF) v JavaScriptu. Systém souborů UDF je k dispozici v [jazyce C# pro IoT Edge úlohy](./stream-analytics-edge-csharp-udf.md) (Preview).
* Uživatelsky definované agregace (UDA).
* Funkce Azure ML.
* Formát AVRO pro vstup a výstup. V současné době jsou podporovány pouze CSV a JSON.
* Následující operátory SQL:
    * ROZDĚLIT PODLE
    * GetMetadataPropertyValue
* Zásady opožděného doručení

### <a name="runtime-and-hardware-requirements"></a>Požadavky na modul runtime a hardware
Chcete-li spustit Stream Analytics v IoT Edge, potřebujete zařízení, která lze spustit [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics a Azure IoT Edge použít kontejnery **Docker** k poskytnutí přenosného řešení, které běží na více hostitelských operačních systémech (Windows, Linux).

Stream Analytics v IoT Edge jsou k dispozici jako image Windows a Linux, které běží na architekturách X86-64 nebo ARM (Advanced RISC Machine). 


## <a name="input-and-output"></a>Vstup a výstup

Stream Analytics hraniční úlohy mohou získat vstupy a výstupy z jiných modulů spuštěných v IoT Edge zařízeních. Pokud se chcete připojit z a ke konkrétním modulům, můžete nakonfigurovat konfiguraci směrování v době nasazení. Další informace jsou popsány v [dokumentaci k kompozici modulu IoT Edge](../iot-edge/module-composition.md).

Pro vstupy i výstupy se podporují formáty CSV a JSON.

Pro každý vstupní a výstupní datový proud, který vytvoříte v úloze Stream Analytics, se v nasazeném modulu vytvoří odpovídající koncový bod. Tyto koncové body lze použít v cestách nasazení.

Podporované typy vstupu streamu jsou:
* Hraniční centrum
* Centrum událostí
* IoT Hub

Podporované typy výstupu streamu jsou:
* Hraniční centrum
* SQL Database
* Centrum událostí
* Blob Storage/ADLS Gen2

Reference Input podporuje typ referenčního souboru. K dalším výstupům se dá dostat pomocí podřízené úlohy cloudu. Například úloha Stream Analytics hostovaná na Edge odesílá výstup do hraničního centra, které pak může odeslat výstup do IoT Hub. Druhou úlohu Azure Stream Analytics hostované v cloudu můžete použít se vstupem z IoT Hub a výstupem do Power BI nebo jiného typu výstupu.

## <a name="license-and-third-party-notices"></a>Licence a oznámení třetích stran
* [Azure Stream Analytics na IoT Edge licence](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Oznámení třetích stran pro Azure Stream Analytics na IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informace o imagi Azure Stream Analytics modulu 

Informace o této verzi byly naposledy aktualizovány v 2020-09-21:

- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platformy
      - Architektura: amd64
      - operační systém: Linux
 
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platformy
      - Architektura: ARM
      - operační systém: Linux
 
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platformy
      - Architektura: arm64
      - operační systém: Linux
      
      
## <a name="get-help"></a>Získání pomoci
Pokud chcete získat další pomoc, zkuste [Azure Stream Analytics na stránce s dotazem pro Microsoft Q&](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Kurz IoT Edge Stream Analytics](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementace CI/CD pro Stream Analytics pomocí rozhraní API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
