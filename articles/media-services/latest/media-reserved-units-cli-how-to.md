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
ms.date: 04/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b1a79f2798fc98fd7361c47788c79e329e2cb827
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556192"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Azure Media Services umožňuje škálování zpracování médií ve svém účtu tím, že spravuje rezervované jednotky médií (použité položky). Použité položky určit rychlost, pomocí kterého se zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2**, nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. 

Kromě určení typu rezervované jednotky, můžete zadat účet zřídit s rezervovaných jednotek. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud má váš účet pak pět média úlohy poběží současně po dobu pěti rezervovaných jednotek, jako jsou úkoly ke zpracování. Ve zbývajících úkolech počká ve frontě a bude získat, vyberou se pro zpracování postupně, kdy spuštěná úloha dokončí. Pokud účet nemá žádné rezervované jednotky zřízené, pak úlohy neexistoval, použije postupně. V takovém případě dobu čekání mezi dokončení úkolů a další nazve spuštění bude záviset na dostupnost prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi jednotku rezervovanou pro různé typy

V následující tabulce umožňuje rozhodování při výběru mezi různými rychlostmi kódování. Poskytuje také několik případů srovnávacích testů na [video, které si můžete stáhnout](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) provádět vlastní testy:

|Typ RU|Scénář|Příklad výsledků pro [7 min 1080 p videa](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo nižší rozlišení, není čas citlivé, s nízkými náklady.|Kódování s jednou přenosovou rychlostí SD rozlišení MP4 souboru pomocí "H264 jeden s přenosovou rychlostí SD 16 x 9" trvá asi 7 minut.|
| **S2**|S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Normální využívání SD a HD, High Density kódování.|Kódování pomocí "H264 s jednou přenosovou rychlostí 720p" přednastavení trvá přibližně 6 minut.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 720p" přednastavení trvá přibližně 12 minut.|
| **S3**|S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Úplné HD, High Density a 4 kB videa s rozlišením. Čas, po vyřízení citlivé a rychlejší kódování.|Kódování pomocí "H264 s jednou přenosovou rychlostí 1080p" přednastavení trvá přibližně 3 minuty.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 1080p" přednastavení trvá přibližně po 8 minutách.|

## <a name="considerations"></a>Požadavky

* Analýza zvuku a videa analýzy úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme typ jednotky S3.
* Pokud používáte sdílený fond, to znamená, bez jakékoli rezervovaných jednotek, pak vaše úkoly kódovat mít stejný výkon stejně jako u jednotek ru S1. Však neexistuje žádná horní mez do doby, kdy vaše úlohy můžete využít ve stavu zařazení do fronty, a v daném okamžiku bude spuštěna maximálně pouze jeden úkol.

Zbytek tohoto článku ukazuje, jak používat [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) škálování použité položky.

> [!NOTE]
> Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).
>
> V současné době nelze použít na webu Azure portal ke správě jiných prostředků v3. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

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

Budou se vám účtovat na počet minut, po které se zřizují rezervovaných jednotek médií ve vašem účtu. K tomu dochází nezávisle na, jestli jsou všechny úlohy spuštěné v účtu. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

* [Kvóty a omezení](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
