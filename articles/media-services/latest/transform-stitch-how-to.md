---
title: Jak sešitovat dva nebo více videosouborů pomocí .NET | Microsoft Docs
description: Tento článek ukazuje, jak sešitovat dva nebo více videosouborů.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281791"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Jak sešitovat dva nebo více videosouborů pomocí .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Sešitovat dva nebo více videosouborů

Následující příklad ukazuje, jak můžete vygenerovat přednastavení pro spojování dvou nebo více videosouborů. Nejběžnějším scénářem je, když chcete do hlavního videa přidat záhlaví nebo přívěs.

> [!NOTE]
> Soubory videa, které jsou upravovány společně, by měly sdílet vlastnosti (rozlišení videa, kmitočet snímků, počet zvukových stop atd.). Měli byste dbát na to, abyste nevzali videa o různých kmitočtech snímků nebo s různými počty zvukových stop.

## <a name="prerequisites"></a>Požadavky

Naklonujte nebo stáhněte [ukázky Media Services .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Kód, na který je odkazováno níže, je umístěn ve [složce EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>Kód .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
