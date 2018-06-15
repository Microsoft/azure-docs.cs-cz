---
title: Azure Media Services kódování kódy chyb | Microsoft Docs
description: Toto téma uvádí chybové kódy, které mohou být vráceny v případě, že došlo k chybě při kódování provádění úlohy...
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 7a1733175f796a0d8c0c0d4247b2db2dd47e4674
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788134"
---
# <a name="encoding-error-codes"></a>Kódování kódy chyb

V následující tabulce jsou uvedeny kódy chyb, které mohou být vráceny v případě, že při kódování provádění úlohy došlo k chybě.  Chcete-li získat podrobnosti o chybě v rozhraní .NET kódu, použijte [detaily chyby](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) třídy. Chcete-li získat podrobnosti o chybě v kódu REST, použijte [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Možné příčiny chyb |
| --- | --- |
| Neznámý |Při provádění úlohy došlo k neznámé chybě. |
| ErrorDownloadingInputAssetMalformedContent |Kategorie chyby, která obsahuje chyby ve stahování vstupní asset například názvy souborů, soubory nulové délky, nesprávný naformátuje a tak dále. |
| ErrorDownloadingInputAssetServiceFailure |Kategorie chyby, které pokrývá problémy na straně služby – Příklad chyby sítě nebo úložiště při stahování. |
| ErrorParsingConfiguration |Kategorie chyby kde úloha <see cref="MediaTask.PrivateData"/> (konfigurace) není platná, například konfigurace není platný systém přednastavení nebo obsahuje neplatný kód XML. |
| ErrorExecutingTaskMalformedContent |Kategorie chyby během provádění úlohy, kde problémy uvnitř vstupní mediálních souborů způsobit selhání. |
| ErrorExecutingTaskUnsupportedFormat |Kategorie chyby kde procesor médií nemůže zpracovat soubory zadané – formátu média není podporován nebo neodpovídá konfigurace. Například pokusu vytvořit pouze výstup z prostředek, který má jenom video |
| ErrorProcessingTask |Kategorie jiné chyby, které procesor médií, zaznamená při zpracování úloh, které se nevztahují na obsah. |
| ErrorUploadingOutputAsset |Kategorie chyby při odesílání výstupní asset |
| ErrorCancelingTask |Kategorie chyby tak, aby pokrývalo selhání při pokusu o zrušení úlohy |
| TransientError |Kategorie chyby tak, aby pokrývalo přechodné problémy (např. dočasné síťové potíže s Azure Storage) |

Jak získat nápovědu z **Media Services** týmu, otevřete [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Přizpůsobením Media Encoder Standard přednastavení provádět pokročilé úlohy kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
