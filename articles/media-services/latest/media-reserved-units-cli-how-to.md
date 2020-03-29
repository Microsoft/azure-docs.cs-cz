---
title: Použití příkazového příkazového příkazu k škálování rezervovaných jednotek médií – Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak pomocí příkazového příkazového příkazu škálovat zpracování médií pomocí Služby Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970783"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Služba Azure Media Services umožňuje škálovat zpracování médií ve vašem účtu správou rezervovaných jednotek médií (MRU). MrU určují rychlost zpracování úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. 

Kromě určení typu rezervované jednotky můžete zadat zřízení účtu s rezervovanými jednotkami. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má například váš účet pět rezervovaných jednotek, bude současně spuštěno pět úloh médií, pokud budou zpracovány úlohy. Zbývající úkoly budou čekat ve frontě a budou po dokončení spuštěné úlohy vyzvednuty pro zpracování postupně. Pokud účet nemá žádné rezervované jednotky zřízena, pak úkoly budou vyzvednuty postupně. V takovém případě bude čekací doba mezi dokončením jednoho úkolu a dalším zahájením záviset na dostupnosti prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Výběr mezi různými typy rezervovaných jednotek

Následující tabulka vám pomůže rozhodnout při výběru mezi různými rychlostmi kódování. Poskytuje také několik případů [benchmarku](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) na videu, které si můžete stáhnout k provedení vlastních testů:

|Typ ŽP|Scénář|Příklad y pro [video 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kódování jednoho přenosové rychlosti. <br/>Soubory na SD nebo pod rozlišení, není časově citlivé, nízké náklady.|Kódování do jednoho souboru MP4 s přenosovým tokem SD pomocí "H264 Single Bitrate SD 16x9" trvá přibližně 7 minut.|
| **S2**|Kódování s jedním přenosovou rychlostí a vícenásobnou přenosovou rychlostí.<br/>Normální použití pro kódování SD i HD.|Kódování s přednastavením "H264 Single Bitrate 720p" trvá přibližně 6 minut.<br/><br/>Kódování s přednastavením "H264 Multiple Bitrate 720p" trvá přibližně 12 minut.|
| **S3**|Kódování s jedním přenosovou rychlostí a vícenásobnou přenosovou rychlostí.<br/>Videa s rozlišením Full HD a 4K. Časově citlivé, rychlejší kódování obratu.|Kódování s přednastavením "H264 Single Bitrate 1080p" trvá přibližně 3 minuty.<br/><br/>Kódování s přednastavením "H264 Multiple Bitrate 1080p" trvá přibližně 8 minut.|

## <a name="considerations"></a>Požadavky

* Pro úlohy analýzy zvuku a analýzy videa, které jsou spuštěny službou Media Services v3 nebo Video Indexer, je typ jednotky S3 vysoce doporučeno.
* Pokud používáte sdílený fond, to znamená bez rezervovaných jednotek, pak vaše úlohy kódování mají stejný výkon jako u jednotek Ru S1. Neexistuje však žádná horní mez času, který mohou úkoly strávit ve stavu ve frontě, a v daném okamžiku bude spuštěna maximálně jedna úloha.

Zbývající část článku ukazuje, jak používat [media services v3 CLI](https://aka.ms/ams-v3-cli-ref) pro škálování MRU.

> [!NOTE]
> Pro úlohy analýzy zvuku a analýzy videa, které jsou aktivované službou Media Services v3 nebo Video Indexerem, důrazně doporučujeme zřídit váš účet s 10 rezervovanými jednotkami S3. Pokud potřebujete více než 10 OBJEKTŮ MRU S3, otevřete lístek podpory pomocí [portálu Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky 

[Vytvořte účet mediálních služeb](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Jednotky rezervovaných médií s vyvažovacím pravomocem

Spusťte příkaz `mru`.

Následující příkaz [mru účtu az ams](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) nastaví rezervované jednotky médií na účtu "amsaccount" pomocí parametrů **count** a **type.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturace

Poplatky vám budou účtovány na základě počtu minut, po které jsou rezervované jednotky médií zřízeny ve vašem účtu. K tomu dochází nezávisle na tom, zda jsou ve vašem účtu spuštěny nějaké úlohy. Podrobné vysvětlení naleznete v části Nejčastější dotazy na stránce [s cenami mediálních služeb.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Viz také

* [Kvóty a omezení](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
