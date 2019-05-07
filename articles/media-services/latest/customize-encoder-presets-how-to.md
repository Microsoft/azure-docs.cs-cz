---
title: Kódování vlastních transformací pomocí Media Services v3 .NET – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Azure Media Services v3 kódování vlastních transformací pomocí .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148341"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Kódování s vlastní transformace – .NET

Při kódování pomocí Azure Media Services, můžete začít rychle s jedním z doporučených předdefinované předvolby podle doporučené postupy, jak je ukázáno v [streamování souborů](stream-files-tutorial-with-api.md) kurzu. Můžete také vytvořit vlastní přednastavení cílit na konkrétní požadavky scénáře nebo zařízení.

## <a name="considerations"></a>Požadavky

Při vytváření vlastní předvolby, platí následující aspekty:

* Všechny hodnoty pro výšku a šířku na AVC obsahu musí být násobkem 4.
* V Azure Media Services v3 kódování přenosových rychlostí jsou všechny bity za sekundu. Tím se liší od přednastavení pomocí rozhraní API v2, který používá kilobitů za sekundu za jednotku. Například pokud přenosovými rychlostmi ve verzi v2 byl zadán jako 128 (kilobitů za sekundu), ve verzi 3 to se nastavuje na 128000 (bitů za sekundu).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Stažení ukázky

Naklonujte úložiště GitHub obsahující úplnou ukázku .NET Core k počítači pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Vlastní předvolby ukázka se nachází v [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) složky.

## <a name="create-a-transform-with-a-custom-preset"></a>Vytvoření transformace s vlastní předvolba 

Při vytváření nového [transformace](https://docs.microsoft.com/rest/api/media/transforms), budete muset určit, co chcete, aby vytvoří jako výstup. Objekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Přednastavení** popisuje podrobný postup zpracování videa nebo zvukový operací, které se mají použít ke generování požadované **TransformOutput**. Následující **TransformOutput** vytvoří vlastní nastavení výstupní kodek a vrstvy.

Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže). V Media Services v3 **získat** metody u entit vrací **null** Pokud entita neexistuje (velká a malá písmena kontrolu název).

### <a name="example"></a>Příklad:

Následující příklad definuje sadu výstupy, které chcete generovat, když se používá tato transformace. Nejprve přidáme vrstvu AacAudio pro zvuk kódování a dvě vrstvy H264Video pro kódování videa. Ve vrstvách videa jsme přiřadit popisky, takže je možné v názvu výstupního souboru. V dalším kroku chceme výstup tak, aby zahrnoval miniatury. V níže uvedeném příkladu určíme obrázky ve formátu PNG, vygeneruje ve výši 50 % rozlišení vstupního videa a na tři časová razítka – {25 %, 50 %, 75} délky vstupního videa. A konečně, My určete formát pro výstupní soubory – jeden pro videa a zvuku a druhou pro miniaturu. Vzhledem k tomu, že máme několik H264Layers, musíme použít makra, která vytvoří jedinečné názvy jednu vrstvu. Můžeme použít `{Label}` nebo `{Bitrate}` – makro, příklad ukazuje bývalé.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Další postup

[Datové proudy souborů](stream-files-tutorial-with-api.md) 
