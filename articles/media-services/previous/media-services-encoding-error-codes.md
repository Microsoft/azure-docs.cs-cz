---
title: Kódy chyb kódování Mediální služby Azure | Dokumenty společnosti Microsoft
description: Toto téma obsahuje seznam kódů chyb, které by mohly být vráceny v případě, že došlo k chybě během provádění úlohkódování..
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64709524"
---
# <a name="encoding-error-codes"></a>Kódy chyb kódování

V následující tabulce jsou uvedeny kódy chyb, které by mohly být vráceny v případě, že došlo k chybě během provádění úlohkódování.  Chcete-li získat podrobnosti o chybě v kódu .NET, použijte třídu [ErrorDetails.](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) Chcete-li získat podrobnosti o chybě v kódu REST, použijte [rozhraní ERRORDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Možné příčiny chyby |
| --- | --- |
| Není známo |Neznámá chyba při provádění úlohy |
| ErrorDownloadingInputAssetMalformedContent |Kategorie chyb, která zahrnuje chyby při stahování vstupního datového zdroje, jako jsou chybné názvy souborů, soubory nulové délky, nesprávné formáty a tak dále. |
| ChybaStahováníSlužby InputAssetService |Kategorie chyb, která se týká problémů na straně služby - například chyby sítě nebo úložiště při stahování. |
| Chybová konfigurace |Kategorie chyb, kde \<úkol viz cref="MediaTask.PrivateData"/> (konfigurace) není platný, například konfigurace není platný systém přednastavení nebo obsahuje neplatný XML. |
| ErrorExecutingTaskMalformedContent |Kategorie chyb během provádění úlohy, kde problémy uvnitř vstupních mediálních souborů způsobit selhání. |
| ChybaProvádění úlohNepodporovaný formát |Kategorie chyb, kdy mediální procesor nemůže zpracovat zadané soubory - formát média není podporován nebo neodpovídá konfiguraci. Například při pokusu o vytvoření zvukového výstupu z datového zdroje, který má pouze video |
| ChybaProcessingTask |Kategorie dalších chyb, které procesor médií narazí při zpracování úlohy, které nesouvisejí s obsahem. |
| ErrorUploadingOutputAsset |Kategorie chyb při nahrávání výstupního datového zdroje |
| ChybaZrušení úlohy |Kategorie chyb, které mají pokrýt chyby při pokusu o zrušení úlohy |
| Chyba přechodu |Kategorie chyb, které pokrývají přechodné problémy (např. dočasné problémy se sítí s Azure Storage) |

Chcete-li získat pomoc od týmu **Media Services,** otevřete [lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením standardních přednastavení kodéru médií](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
