---
title: Kódování vlastních transformací pomocí Azure Media Services v3 | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódování vlastní transformace.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: c3318020b54beb121ac48d993a8ce794ac8e377f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376631"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Kódování s vlastní transformace

Při kódování pomocí Azure Media Services, můžete začít rychle s jedním z doporučených předdefinované předvolby podle doporučené postupy, jak je ukázáno v [streamování souborů](stream-files-tutorial-with-api.md) kurzu, nebo se můžete rozhodnout vytvářet vlastní přednastavení cílit na konkrétní požadavky scénáře nebo zařízení. 

> [!Note]
> V Azure Media Services v3 kódování přenosové rychlosti jsou všechny bity za sekundu. To se liší od v2 REST, které přednastavení kodéru Media Encoder Standard. Například s přenosovou rychlostí v v2 by byl zadán jako 128, ale ve verzi 3 by 128000.

## <a name="download-the-sample"></a>Stažení ukázky

Naklonujte úložiště GitHub obsahující úplnou ukázku .NET Core k počítači pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Vlastní předvolby ukázka se nachází v [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) složky.

## <a name="create-a-transform-with-a-custom-preset"></a>Vytvoření transformace s vlastní předvolba 

Při vytváření nového [transformace](https://docs.microsoft.com/rest/api/media/transforms), budete muset určit, co chcete, aby vytvoří jako výstup. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Následující **TransformOutput** vytvoří vlastní nastavení výstupní kodek a vrstvy.

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  V Media Services v3 **získat** metody u entit vrací **null** Pokud entita neexistuje (velká a malá písmena kontrolu název).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Další postup

[Datové proudy souborů](stream-files-tutorial-with-api.md) 
