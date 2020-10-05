---
title: Odvození schématu metadat – Azure
description: V tomto článku se dozvíte o schématu odvozených metadat.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 6239713fc92b75b8ed026a8f04953e92a24c4596
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691802"
---
# <a name="inference-metadata-schema"></a>Schéma odvozování metadat 

Každý objekt odvození bez ohledu na použití kontraktu HTTP nebo kontraktu založeného na gRPC se řídí objektovým modelem popsaným v tomto tématu.

## <a name="object-model"></a>Objektový model

![Objektový model](./media/inference-metadata-schema/object-model.png)
 
|Definice typu|Popis|
|---|---|
|Značka|Značka nebo popisek spojený s výsledkem Alng s označováním, ještě získáte hodnotu spolehlivosti přidruženou ke značce.|
|Atribut|Další atributy přidružené k výsledku. Můžete přidat nové atributy, které obdržíte z modulu Inferencing, spolu s hodnotou spolehlivosti.|
|Seznam atributů|Seznam volitelných atributů|
|Obdélník|Obdélníková oblast relativní vzhledem k levému hornímu rohu obrázku Požadované vlastnosti budou být "Délka", "Šířka", Výška "a" horní hrana vzdálenosti od počátku ".|
|Classification|Popisek třídění je často použitelný pro celou ukázku. Pomocí "značky" můžete klasifikovat výsledek.|
|Entita|V ukázce se zjistila nebo identifikovala entita. Když modul Inferencing zjistí entitu, získá "tag", další atributy, které byly odvoditelné a jsou vráceny souřadnice obdélníkového pole kolem nalezené nalezené entity.|
|Událost|V ukázce byla zjištěna událost. Při zjištění události v ukázce se vrátí název události a vlastnosti specifické pro událost.|
|Pohybu|V ukázce byl zjištěn pohyb. Při zjištění pohybu v ukázce se vrátí souřadnice obdélníkového ohraničovacího rámečku, kde je pohyb zjištěn.|
|Text|Vrátí se text přidružený k ukázce spolu s časovým razítkem začátku a konce textu.|
|Ostatní|Vrátí další obecné informace o datové části.|

Následující příklad obsahuje jednu událost s některými podporovanými typy odvození:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Další kroky

- [Kontrakt dat gRPC](grpc-data-contract.md)
- [Kontrakt dat HTTP](http-data-contract.md)
