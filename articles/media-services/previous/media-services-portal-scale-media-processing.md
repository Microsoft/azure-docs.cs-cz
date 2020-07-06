---
title: Škálování zpracování médií pomocí Azure Portal | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky při škálování zpracování médií pomocí Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "61127529"
---
# <a name="change-the-reserved-unit-type"></a>Změna typu rezervované jednotky
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Azure Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Přehled

Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**.

Kromě určení typu rezervované jednotky můžete určit, že se má účet zřídit pomocí **rezervovaných jednotek** (ru). Počet zřízených RU určuje počet úloh médií, které je možné v daném účtu zpracovávat současně.

>[!NOTE]
>RU fungují pro paralelní provádění veškerého zpracování médií, včetně úloh indexování pomocí Azure Media Indexeru. Ale na rozdíl od kódování se úlohy indexování s rychlejšími rezervovanými jednotkami nezpracovávají rychleji.

> [!IMPORTANT]
> Přečtěte si téma [Přehled](media-services-scale-media-processing-overview.md) , kde najdete další informace o škálování v tématu zpracování multimédií.
> 
> 

## <a name="scale-media-processing"></a>Škálování zpracování médií
Pokud chcete změnit typ rezervované jednotky a počet rezervovaných jednotek, udělejte toto:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **rezervované jednotky médií**.
   
    Pokud chcete změnit počet rezervovaných jednotek pro vybraný typ rezervované jednotky, použijte v horní části obrazovky posuvník pro **jednotky s médii** .
   
    Chcete-li změnit **typ REZERVOVANÉ jednotky**, klikněte na panel **rychlost vyhrazeného zpracování jednotek** . Pak vyberte cenovou úroveň, kterou potřebujete: S1, S2 nebo S3.
   
3. Stisknutím tlačítka ULOŽIT uložte provedené změny.
   
    Nové rezervované jednotky jsou přiděleny při stisknutí tlačítka Uložit.

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

