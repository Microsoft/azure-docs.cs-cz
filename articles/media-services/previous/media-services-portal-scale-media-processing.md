---
title: Škálování zpracování médií pomocí webu Azure portal | Dokumentace Microsoftu
description: Tento kurz vás provede kroky zpracování, pomocí webu Azure portal škálování médií.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 810d1bf6a550539b402721d089d8b041cfb48634
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991445"
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

Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2**, nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**.

Kromě určení typu rezervované jednotky můžete určit, že chcete účet zřídit s **rezervovanými jednotkami** (RU). Počet zřízených RU určuje počet úloh médií, které je možné v daném účtu zpracovávat současně.

>[!NOTE]
>RU fungují pro paralelní provádění veškerého zpracování médií, včetně úloh indexování pomocí Azure Media Indexeru. Ale na rozdíl od kódování se úlohy indexování s rychlejšími rezervovanými jednotkami nezpracovávají rychleji.

> [!IMPORTANT]
> Přečtěte si [přehled](media-services-scale-media-processing-overview.md) tématu zobrazíte další informace o škálování tématu zpracování médií.
> 
> 

## <a name="scale-media-processing"></a>Škálování zpracování médií
Změna typu rezervované jednotky a počtu rezervovaných jednotek, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V **nastavení** okně **rezervované jednotky médií**.
   
    Chcete-li změnit počet jednotek rezervovaných pro jednotku rezervovanou pro vybraný typ, použijte **jednotky zpracování médií** posuvník v horní části obrazovky.
   
    Chcete-li změnit **typu REZERVOVANÉ jednotky**, klikněte na **rychlost rezervovaných jednotek zpracování** panelu. Vyberte cenovou úroveň, které potřebujete: S1, S2 nebo S3.
   
3. Stisknutím tlačítka ULOŽIT uložte provedené změny.
   
    Nová vyhrazená jednotky jsou přiděleny po stisknutí klávesy uložit.

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

