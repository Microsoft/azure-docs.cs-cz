---
title: Azure Media Services kódy chyb kódování | Microsoft Docs
description: Toto téma obsahuje seznam chybových kódů, které by mohly být vráceny v případě, že při provádění úlohy kódování došlo k chybě..
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "64709524"
---
# <a name="encoding-error-codes"></a>Kódy chyb kódování

V následující tabulce jsou uvedeny kódy chyb, které by mohly být vráceny v případě, že došlo k chybě při provádění úlohy kódování.  Chcete-li získat podrobnosti o chybě v kódu .NET, použijte třídu [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Pokud chcete získat podrobnosti o chybě v kódu REST, použijte REST API [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

| ErrorDetail. Code | Možné příčiny chyby |
| --- | --- |
| Není známo |Neznámá chyba při provádění úlohy |
| ErrorDownloadingInputAssetMalformedContent |Kategorie chyb, které pokrývají chyby při stahování vstupních assetů, jako jsou například chybné názvy souborů, soubory s nulovou délkou, nesprávné formáty atd. |
| ErrorDownloadingInputAssetServiceFailure |Kategorie chyb, které pokrývají problémy na straně služby – například chyby sítě nebo úložiště při stahování. |
| ErrorParsingConfiguration |Kategorie chyb, kde Task \<uvidí cref = "MediaTask. PrivateData"/> (konfigurace) není platná, například konfigurace není platná předvolba systému nebo obsahuje neplatný kód XML. |
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
