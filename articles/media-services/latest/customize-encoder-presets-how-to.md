---
title: Zakódovat vlastní transformaci pomocí media services v3 .NET - Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódovat vlastní transformaci pomocí rozhraní .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068033"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Kódování pomocí vlastní transformace - .NET

Při kódování pomocí Azure Media Services můžete rychle začít s jedním z doporučených předvoleb založených na osvědčených postupech v oboru, jak je znázorněno v kurzu [Streamování souborů.](stream-files-tutorial-with-api.md) Můžete také vytvořit vlastní přednastavení pro cílí na konkrétní scénář nebo požadavky na zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující aspekty:

* Všechny hodnoty pro výšku a šířku obsahu AVC musí být násobkem 4.
* Ve službě Azure Media Services v3 jsou všechny přetáčicí přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi v2 API, který používá kilobitů za sekundu jako jednotku. Například pokud byl určen přenosový tok v 2 jako 128 (kilobitů za sekundu), ve v3 by byla nastavena na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Stažení ukázky

Klonujte úložiště GitHub, které obsahuje úplnou ukázku jádra .NET, do počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Vlastní ukázka přednastavení je umístěna ve složce [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Vytvoření transformace s vlastním přednastavením 

Při vytváření nové [transformace](https://docs.microsoft.com/rest/api/media/transforms)je třeba určit, co má být k onomu výstupu. Objekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Přednastavení** popisuje podrobné pokyny operací zpracování videa a/nebo zvuku, které mají být použity ke generování požadovaného **transformoutput**. Následující **TransformOutput** vytvoří vlastní nastavení kodeku a výstupu vrstvy.

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže). V Media Services **v3, Get** metody na entity vrátí **null,** pokud entita neexistuje (malá a velká písmena kontrola na název).

### <a name="example"></a>Příklad

Následující příklad definuje sadu výstupů, které chceme generovat při použití této transformace. Nejprve přidáme vrstvu AacAudio pro kódování zvuku a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa přiřazujeme popisky tak, aby je bylo možné použít v názvech výstupních souborů. Dále chceme, aby výstup obsahoval také miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, generované při 50 % rozlišení vstupního videa a na třech časových razítkách - {25%, 50%, 75} délky vstupního videa. Nakonec určíme formát výstupních souborů - jeden pro video + audio a druhý pro miniatury. Vzhledem k tomu, že máme více H264Layers, musíme použít makra, které produkují jedinečné názvy na vrstvu. Můžeme použít `{Label}` buď `{Bitrate}` nebo makro, příklad ukazuje bývalý.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md) 
