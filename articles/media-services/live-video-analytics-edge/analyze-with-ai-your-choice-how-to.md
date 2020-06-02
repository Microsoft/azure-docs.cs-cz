---
title: Analýza živého videa pomocí AI dle vašeho výběru – Azure
description: V tomto článku se naučíte, jak vytvořit modul IoT Edge, který se dá integrovat se službou Live video Analytics v IoT Edge k analýze živého videa pomocí modelu počítačové vize dle vašeho výběru.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261258"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analýza živého videa pomocí AI dle vašeho výběru

V tomto článku se naučíte, jak vytvořit modul IoT Edge, který se dá integrovat se službou Live video Analytics v IoT Edge k analýze živého videa pomocí modelu počítačové vize dle vašeho výběru. 

## <a name="pre-reading"></a>Před čtením

[Koncept Media graphu](media-graph-concept.md)

## <a name="media-graph-extension"></a>Rozšíření Media graphu

Live video Analytics na IoT Edge definuje model rozšiřitelnosti, který umožňuje rozšířit možnosti zpracování multimediálního grafu na vaše vlastní komponenty pro analýzu multimédií prostřednictvím rozšíření grafu. Vaše komponenta Analytics může využívat tradiční techniky zpracování obrazu nebo modely AI pro počítačové vidění. Rozšíření grafu jsou povolena zahrnutím uzlu [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) do mediálního grafu. Procesor rozšíření HTTP může přenášet snímky videa na koncový bod HTTP určený vámi a funguje jako rozhraní vaší komponenty prostřednictvím. Připojení se dá vytvořit v místním nebo vzdáleném koncovém bodě a v případě potřeby ho můžete zabezpečit pomocí ověřování a šifrování TLS. Kromě toho procesor umožňuje volitelné škálování a kódování snímků videa před jejich přeposíláním.

Můžete zvolit spuštění modelu odvození podle vašeho výběru na jakémkoli dostupném modulu runtime pro odvození, jako je například ONNX, TensorFlow, PyTorch nebo jiné na svém vlastním kontejneru Docker. Můžete také použít jazyk programu a knihovny podle vašeho výběru k vystavení imagí Inferencing, i když je server HTTP na vašem vlastním kontejneru. Kontejner Inferencing by se měl nasadit společně s modulem Edge (video Analytics Edge) pro nejlepší výkon a pak se bude volat přes procesor rozšíření HTTP zahrnutý ve vaší topologii grafu.
Frekvence volání do vlastního modulu se taky dá omezit tak, že do procesoru rozšíření HTTP volitelně přidáte [procesor snímače pohybu](media-graph-concept.md#motion-detection-processor) a přesměruje [procesor filtru snímkové frekvence](media-graph-concept.md#frame-rate-filter-processor) .

Následující diagram znázorňuje tok dat vysoké úrovně:

![Hraniční zařízení](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Díky tomu můžete analyzovat video pomocí modelů AI podle vašeho výběru tak, aby vyhovovaly vašim jedinečným obchodním potřebám. Modely AI by mohly být z open source komunity nebo od specializovaného poskytovatele AI.

## <a name="media-graph-http-extension-contract-definitions"></a>Definice kontraktů rozšíření HTTP pro Media Graph

Tato část definuje kontrakt HTTP definující tok dat.

### <a name="http-extensibility-protocol"></a>Protokol rozšiřitelnosti HTTP  

Kontrakt HTTP je definován následujícím způsobem:

* Váš modul funguje jako server HTTP.
* Live video Analytics na IoT Edge modul funguje jako klient HTTP.

### <a name="request"></a>Žádost

Požadavky z modulu Live video Analytics do vašeho modulu budou vypadat takto:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Přijmout|Application/JSON,*/*|
|Autorizace| Basic, Digest, nosič (prostřednictvím podpory vlastních hlaviček)|
|Typ obsahu|image/jpeg<br/>image/png<br/>image/bmp<br/>Obrázek/x-RAW|
|Délka obsahu|Délka těla v bajtech|
|User-Agent|Azure Media Services|
|Text|Bajty obrázku, binární kódování v jednom z podporovaných typů obsahu.|

#### <a name="example"></a>Příklad

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Odpověď

Odpovědi z vašeho modulu na živý modul video Analytics by měly být následující.

|||
|---|---|
|Stavové kódy|200 OK – nalezené výsledky odvození<br/>204 bez obsahu – žádný obsah nenalezený v AI<br/>400 Chybný požadavek – neočekávané<br/>500 interní chyba serveru – neočekávané<br/>503 Server je zaneprázdněný – AMS se vrátí na základě hlavičky "opakovat po" nebo na základě výchozí doby v případě, že není přednastavená hlavička případu.|
|Typ obsahu|application/json|
|Délka obsahu|    Délka těla v bajtech|
|Text|Objekt JSON s jednou vlastností "odvozování".|

#### <a name="example"></a>Příklad

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

Důrazně doporučujeme, aby se odpovědi vracely pomocí platných dokumentů JSON po předem zavedeném schématu definovaném níže. To zajistí lepší spolupráci s ostatními komponentami a možné budoucí možnosti přidané do modulu Live video Analytics.

Pokud váš modul vrátí odpověď, kde typ obsahu není "Application/JSON", Analýza živého videa zakóduje zprávu jako základní obsah 64 a serializovat jako neprůhlednou datovou část JSON.

Pokud váš modul vrátí odpověď s typem obsahu jako "Application/JSON", ale schéma JSON nenásleduje za [odvozeným schématem metadat](#inference-metadata-schema), bude datová část zprávy předána prostřednictvím kanálu, ale bude snížena interoperabilita.

> [!NOTE]
> Pokud váš modul neprodukuje žádný výsledek, měl by vrátit stavový kód HTTP 204 (žádný obsah) s prázdným textem odpovědi. Live video Analytics to bude rozumět jako prázdný výsledek a nepředá událost v rámci kanálu.

### <a name="inference-metadata-schema"></a>Odvození schématu metadat

Každý objekt odvození se řídí tímto nadmnožinou schématu:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Kontrakty dat – hierarchie tříd

![Kontrakty dat – hierarchie tříd](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Příklady  

Následující příklad obsahuje jednu událost se všemi podporovanými typy odvození:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Ukázkové moduly rozšíření HTTP

V [úložišti GitHub Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)můžete najít několik ukázkových modulů rozšíření http. Jedna z těchto [ukázek analýzy videí](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) ukazuje, jak pomocí modelu [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) sestavit modul IoT Edge pro detekci objektů. Stejný přístup můžete použít k vytvoření vlastního modulu s modelem AI podle vašeho výběru.

## <a name="next-steps"></a>Další kroky

[Řešení potíží](troubleshoot-how-to.md)
