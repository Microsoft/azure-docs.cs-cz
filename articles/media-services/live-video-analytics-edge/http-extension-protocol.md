---
title: Protokol rozšíření HTTP – Azure
description: V tomto článku se dozvíte o použití protokolu rozšíření HTTP k posílání zpráv mezi modulem Live video Analytics a modulem AI nebo CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: c3a9b5b70467479fdb9b1473a1f6738eaa8a7e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455886"
---
# <a name="http-extension-protocol"></a>Protokol rozšíření HTTP

Live video Analytics na IoT Edge umožňuje rozšířit možnosti zpracování mediálního grafu prostřednictvím [uzlu rozšíření grafu](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept). Pokud jako uzel rozšíření používáte procesor rozšíření HTTP, pak je komunikace mezi modulem Live video Analytics a vaším modulem AI nebo CV přes HTTP.

V tomto článku se dozvíte o použití protokolu rozšíření HTTP k posílání zpráv mezi modulem Live video Analytics a modulem AI nebo CV. 

Kontrakt HTTP je definován mezi následujícími dvěma součástmi:

* Server HTTP
* Live video Analytics v modulu IoT Edge funguje jako klient HTTP.

## <a name="request"></a>Žádost

Požadavky z modulu Live video Analytics na váš server HTTP budou vypadat takto:

|Klíč|Hodnota|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Přijmout|Application/JSON,  */*|
|Autorizace|Basic, Digest, nosič (prostřednictvím podpory vlastních hlaviček)|
|Typ obsahu|image/jpeg<br/>image/png<br/>image/bmp<br/>Obrázek/x-RAW|
|Délka těla Content-Length (v bajtech)|
|User-Agent|Azure Media Services|
|Text|Bajty obrázku, binární kódování v jednom z podporovaných typů obsahu.|

### <a name="example"></a>Příklad

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Odpověď

Odpovědi z vašeho modulu na živý modul video Analytics by měly být následující:

|Klíč|Hodnota|
|---|---|
|Stavové kódy|200 OK – nalezené výsledky odvození<br/>204 bez obsahu – žádný obsah nenalezený v AI<br/>400 Chybný požadavek – neočekávané<br/>500 interní chyba serveru – neočekávané<br/>503 Server je zaneprázdněný – AMS se vrátí na základě hlavičky "opakovat po" nebo na základě výchozí doby v případě, že není přednastavená hlavička případu.|
|Typ obsahu|application/json|
|Délka obsahu|Délka těla v bajtech|
|Text|Objekt JSON s jednou vlastností "odvozování".|

### <a name="example"></a>Příklad

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Důrazně doporučujeme, aby se odpovědi vracely pomocí platných dokumentů JSON po předem zavedeném schématu definovaném podle [modelu objektu schématu pro odvození metadat](/azure/media-services/live-video-analytics-edge/inference-metadata-schema). To zajistí lepší spolupráci s ostatními komponentami a možné budoucí možnosti přidané do modulu Live video Analytics.

Pokud váš modul vrátí odpověď, kde typ obsahu není "Application/JSON", Analýza živého videa zakóduje zprávu jako základní obsah 64 a serializovat jako neprůhlednou datovou část JSON.

Pokud váš modul vrátí odpověď s typem obsahu jako "Application/JSON", ale schéma JSON nenásleduje za odvozeným schématem metadat, bude datová část zprávy předána prostřednictvím kanálu, ale bude snížena interoperabilita. Podrobné a aktuální informace o schématu odvozených metadat najdete [tady](/azure/media-services/live-video-analytics-edge/inference-metadata-schema) .

> [!NOTE]
> Pokud váš modul neprodukuje žádný výsledek, měl by vrátit stavový kód HTTP 204 (žádný obsah) s prázdným textem odpovědi. Live video Analytics to bude rozumět jako prázdný výsledek a nepředá událost v rámci kanálu.


## <a name="next-steps"></a>Další kroky

[Protokol rozšíření gRPC](./grpc-extension-protocol.md)
