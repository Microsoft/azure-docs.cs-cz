---
title: Azure Media Services kódy chyb kódování | Microsoft Docs
description: Toto téma obsahuje seznam chybových kódů, které by mohly být vráceny v případě, že při provádění úlohy kódování došlo k chybě..
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013478"
---
# <a name="encoding-error-codes"></a>Kódy chyb kódování

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

V následující tabulce jsou uvedeny kódy chyb, které by mohly být vráceny v případě, že došlo k chybě při provádění úlohy kódování.  Chcete-li získat podrobnosti o chybě v kódu .NET, použijte třídu [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) . Pokud chcete získat podrobnosti o chybě v kódu REST, použijte REST API [ErrorDetail](/rest/api/media/operations/errordetail) .

| ErrorDetail. Code | Možné příčiny chyby |
| --- | --- |
| Neznámý |Neznámá chyba při provádění úlohy |
| ErrorDownloadingInputAssetMalformedContent |Kategorie chyb, které pokrývají chyby při stahování vstupních assetů, jako jsou například chybné názvy souborů, soubory s nulovou délkou, nesprávné formáty atd. |
| ErrorDownloadingInputAssetServiceFailure |Kategorie chyb, které pokrývají problémy na straně služby – například chyby sítě nebo úložiště při stahování. |
| ErrorParsingConfiguration |Kategorie chyb, kde úloha \<see cref="MediaTask.PrivateData"/> (konfigurace) není platná, například konfigurace není platná pro Předvolby systému nebo obsahuje neplatný kód XML. |
| ErrorExecutingTaskMalformedContent |Kategorie chyb během provádění úlohy, kde problémy uvnitř vstupních mediálních souborů způsobují selhání. |
| ErrorExecutingTaskUnsupportedFormat |Kategorie chyb, ve kterých procesor médií nemůže zpracovat zadané soubory – formát média není podporován nebo se neshoduje s konfigurací. Například pokus o vystavení výstupu jenom pro zvukový výstup z assetu, který má jenom video. |
| ErrorProcessingTask |Kategorie dalších chyb, ke kterým má procesor multimédií během zpracování úlohy, která nesouvisí s obsahem. |
| ErrorUploadingOutputAsset |Kategorie chyb při nahrávání výstupního prostředku |
| ErrorCancelingTask |Kategorie chyb, které mají pokrýt selhání při pokusu o zrušení úlohy |
| TransientError |Kategorie chyb, které pokrývají přechodné problémy (např. dočasné problémy se sítí s Azure Storage) |

Pokud chcete získat nápovědu od **Media Services** týmu, otevřete [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením Media Encoder Standard přednastavení](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
