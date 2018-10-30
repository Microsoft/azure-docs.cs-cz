---
title: Azure Media Services encoding kódy chyb | Dokumentace Microsoftu
description: Toto téma uvádí kódy chyb, které může být vrácen v případě, že při kódování provádění úlohy došlo k chybě...
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: bc1a53ba9e3630c7f985ce5c78bc4c874c70b26e
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230382"
---
# <a name="encoding-error-codes"></a>Kódy chyb kódování

Následující tabulka uvádí chybové kódy, které může být vrácen v případě, že při kódování provádění úlohy došlo k chybě.  Chcete-li získat podrobné informace o chybě ve vašem kódu .NET, použijte [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) třídy. Chcete-li získat podrobnosti o chybě ve vašem kódu REST, použijte [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) rozhraní REST API.

| ErrorDetail.Code | Možné příčiny chyby |
| --- | --- |
| Neznámé |Neznámá chyba při provádění úlohy |
| ErrorDownloadingInputAssetMalformedContent |Kategorie chyby, která obsahuje chyby při stahování vstupní asset, jako jsou názvy souborů, soubory nulovou délku, nesprávné formátuje a tak dále. |
| ErrorDownloadingInputAssetServiceFailure |Kategorie chyby, které pokrývá problémy na straně služby – chyby sítě nebo úložiště příklad při stahování. |
| ErrorParsingConfiguration |Kategorie chyb, ve kterém úlohy <see cref="MediaTask.PrivateData"/> (konfigurace) není platná, například konfigurace není platný systém přednastavení nebo obsahuje neplatný kód XML. |
| ErrorExecutingTaskMalformedContent |Kategorie chyby během provádění úlohy, ve kterém problémy uvnitř vstupními multimediálními soubory způsobit selhání. |
| ErrorExecutingTaskUnsupportedFormat |Kategorie chyby, pokud procesor médií nemůže zpracovat soubory k dispozici – formát multimédií není podporován nebo se neshoduje s nastavením. Například pokusu vygenerovat pouze se zvukem výstup z prostředku, který má pouze video |
| ErrorProcessingTask |Kategorie jiné chyby, které procesor médií, zaznamená během zpracování úlohy, které nesouvisí s obsahem. |
| ErrorUploadingOutputAsset |Kategorie chyby při nahrávání výstupního prostředku |
| ErrorCancelingTask |Kategorie chyby pro selhání při pokusu o zrušení úlohy |
| TransientError |Kategorie chyby pro pokrytí přechodné problémy (např.) dočasné síťové potíže se službou Azure Storage) |

Pokud chcete získat pomoc od **Media Services** týmu, otevřete [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Pokročilé kódovací úlohy provést pomocí přizpůsobení předvoleb Media Encoderu Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
