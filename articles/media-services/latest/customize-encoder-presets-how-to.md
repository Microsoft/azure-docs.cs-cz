---
title: Kódování vlastní transformace pomocí Azure Media Services v3 | Microsoft Docs
description: Toto téma ukazuje, jak používat Azure Media Services v3 ke kódování vlastní transformace.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654874"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Kódování s vlastní transformace

Při kódování službou Azure Media Services, které vám umožní rychle začít s jedním z doporučených předdefinované předvolby podle odvětví osvědčené postupy, jak je předvedeno v [streamování soubory](stream-files-tutorial-with-api.md) kurzu, nebo se můžete rozhodnout k vytvoření vlastní přednastavení pro vaše konkrétní požadavky scénáři nebo zařízení. 

> [!Note]
> V Azure Media Services v3 kódování přenosové rychlosti jsou všechny bity za sekundu. To se liší od v2 REST, který nastaví aplikace Media Encoder Standard. Například přenosovou rychlostí v v2 by byl zadán jako 128, ale v v3 by bylo 128000.

## <a name="download-the-sample"></a>Stažení ukázky

Naklonujte úložiště GitHub, který obsahuje v celé ukázce .NET Core k počítači pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Vlastní přednastavené ukázka se nachází v [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) složky.

## <a name="create-a-transform-with-a-custom-preset"></a>Vytvoření transformace s vlastní přednastavení 

Při vytváření nového [transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co se má vytvořit jako výstup. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Následující **TransformOutput** vytvoří vlastní nastavení výstupní kodeků a vrstva.

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  Ve službě Media Services v3 **získat** metody na entity vrací **null** Pokud entita neexistuje (bez rozlišování velikosti písmen kontrola na název).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Další postup

[Streamování soubory](stream-files-tutorial-with-api.md) 
