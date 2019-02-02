---
title: Značky detekce - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s značky nebo loga detekce pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a8822eb773842df3ea6fd50cd2a6ba36c3c4cd88
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569590"
---
# <a name="brand-detection"></a>Detekce značky

Detekce značky je speciální režim [objektu zjišťování](concept-object-detection.md) , která používá databázi tisíce globální loga pro identifikaci obchodních značek v obrázcích nebo ve videích. Tuto funkci můžete použít třeba ke zjišťování, které značky jsou nejoblíbenější na sociálních sítích nebo nejrozšířenější v umístění média produktu.

Službu pro počítačové zpracování obrazu zjistí, zda jsou v bitové kopii daného; značky loga Pokud ano, vrátí se název značky, skóre spolehlivosti a souřadnice ohraničujícího rámečku kolem loga.

Logo integrované databáze se věnuje oblíbené značky v spotřební elektroniku, oblečení a další. Pokud zjistíte, že služba pro počítačové zpracování obrazu není zjištěna značku, kterou hledáte, můžete, může lépe vyhovovat vytvoření a trénování vlastních pomocí detektoru logo [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) služby.

## <a name="brand-detection-example"></a>Příklad detekce značky

Následující odpověďmi ve formátu JSON ukazuje, co pro počítačové zpracování obrazu vrací při zjišťování značky v příkladu imagí.

![Šedé sweatshirt s Microsoft popisku a na něm logo](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
V některých případech detektor značky vyzvedne, až bude obrázek loga a stylizované název značky jako dva samostatné loga.

![Azure red shirt s Microsoft popisku a na něm logo](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Použití rozhraní API
Funkce detekce značky je součástí [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API. Můžete volat toto rozhraní API prostřednictvím nativní sadou SDK nebo volání REST. Když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec pro obsah `"brands"` oddílu.

* [Rychlé zprovoznění: Analýza obrázku (sadu .NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rychlé zprovoznění: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)