---
title: Pomocí rozhraní příkazového řádku můžete škálovat rezervovaných jednotek médií – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí rozhraní příkazového řádku škálování zpracování médií pomocí Azure Media Services.
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
ms.date: 08/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102951"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Azure Media Services umožňuje škálování zpracování médií ve svém účtu tím, že spravuje rezervované jednotky médií (použité položky). MRUs určuje rychlost zpracování úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2**nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. 

Kromě určení typu rezervované jednotky můžete zadat, aby se účet zřídil rezervovanými jednotkami. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má váš účet například pět rezervovaných jednotek, pak pět mediálních úloh bude spuštěno souběžně, dokud budou zpracovány úkoly. Zbývající úlohy budou čekat ve frontě a budou vyzvednuty pro zpracování po dokončení běžící úlohy. Pokud účet nemá zřízeny žádné rezervované jednotky, budou úkoly postupně vyzvednuty. V tomto případě bude doba čekání mezi dokončením jednoho úkolu a dalším počátkem záviset na dostupnosti prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi různými typy rezervovaných jednotek

Následující tabulka vám pomůže při rozhodování o tom, jak určit různé rychlosti kódování. Poskytuje také několik případů srovnávacích testů na [videu, které můžete stáhnout,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) abyste mohli provádět vlastní testy:

|Typ RU|Scénář|Příklady výsledků pro [video o 7 min](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) .|
|---|---|---|
| **S1**|Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo pod rozlišením, nezávislá na čase, nízké náklady.|Kódování souboru MP4 s jednou přenosovou rychlostí SD pomocí "H264 s jednou přenosovou rychlostí" 16x9 "trvá přibližně 7 minut.|
| **S2**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Normální použití pro kódování SD i HD.|Kódování s přednastavenou H264 Single přenosovou rychlostí 720p trvá přibližně 6 minut.<br/><br/>Kódování s přednastaveným H264 Multiple přenosovou rychlostí 720p trvá přibližně 12 minut.|
| **S3**|Jedna přenosová rychlost a s více přenosovými rychlostmi.<br/>Kompletní videa o rozlišení HD a 4K. Kódování citlivé na čas, rychlejší zadoba vyřízení.|Kódování pomocí přednastavené H264 s jednou přenosovou rychlostí 1080p trvá přibližně 3 minuty.<br/><br/>Kódování s přednastavenou H264 s více přenosovými rychlostmi 1080p trvá přibližně 8 minut.|

## <a name="considerations"></a>Požadavky

* Pro analýzy zvuku a úlohy analýzy videí, které se aktivují Media Services V3 nebo Video Indexer, se důrazně doporučuje typ jednotky S3.
* Pokud používáte sdílený fond, to znamená, že bez rezervovaných jednotek, budou mít vaše úlohy kódování stejný výkon jako u ru S1. Není však k dispozici horní mez doby, kterou mohou úlohy ve stavu zařazeny do fronty, a v jednom okamžiku bude spuštěna pouze jedna úloha.

Ve zbývající části článku se dozvíte, jak pomocí [Media Services V3 CLI](https://aka.ms/ams-v3-cli-ref) škálovat MRUs.

> [!NOTE]
> Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).
>
> V současné době nemůžete použít Azure Portal ke správě dalších prostředků v3. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Škálování médií rezervované jednotky pomocí rozhraní příkazového řádku

Spusťte příkaz `mru`.

Následující [az ams account naposledy použité položky](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) sets rezervované jednotky médií na "amsaccount" účtu pomocí příkazu **počet** a **typ** parametry.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturace

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu. K tomu dochází nezávisle na tom, zda se ve vašem účtu spouštějí nějaké úlohy. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Viz také:

* [Kvóty a omezení](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
