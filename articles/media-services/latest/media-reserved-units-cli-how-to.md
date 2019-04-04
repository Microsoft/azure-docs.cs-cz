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
ms.date: 03/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0dcfa4e7cd792f61d1620a57330f87c5c86e6c9f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915681"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Služba Azure Media Services umožňuje škálovat zpracování médií ve vašem účtu správou rezervovaných jednotek médií (MRU). Použité položky určit rychlost, pomocí kterého se zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2**, nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. 

Kromě určení typu rezervované jednotky, můžete zadat účet zřídit s rezervovaných jednotek. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud má váš účet pak pět média úlohy poběží současně po dobu pěti rezervovaných jednotek, jako jsou úkoly ke zpracování. Ve zbývajících úkolech počká ve frontě a bude získat, vyberou se pro zpracování postupně, kdy spuštěná úloha dokončí. Pokud účet nemá žádné rezervované jednotky zřízené, pak úlohy neexistoval, použije postupně. V takovém případě dobu čekání mezi dokončení úkolů a další nazve spuštění bude záviset na dostupnost prostředků v systému.

## <a name="choosing-between-different-reserved-unit-types"></a>Volba mezi jednotku rezervovanou pro různé typy

V následující tabulce umožňuje rozhodování při výběru mezi různými rychlostmi kódování. Poskytuje také několik případů srovnávacích testů na [video, které si můžete stáhnout](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) provádět vlastní testy:

|Typ RU|Scénář|Příklad výsledků pro [7 min 1080 p videa](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kódování s jednou přenosovou rychlostí. <br/>Soubory na SD nebo nižší rozlišení, není čas citlivé, s nízkými náklady.|Kódování na jednoduché s přenosovou rychlostí SD řešení soubor MP4 pomocí "H264 jeden s přenosovou rychlostí SD 16 x 9" má 10 minut.|
| **S2**|S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Normální využívání SD a HD, High Density kódování.|Kódování pomocí "H264 s jednou přenosovou rychlostí 720p" přednastavení trvá přibližně 8 minut.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 720p" přednastavení trvá přibližně 16.8 minut.|
| **S3**|S jednou přenosovou rychlostí a více s přenosovou rychlostí s kódováním.<br/>Úplné HD, High Density a 4 kB videa s rozlišením. Čas, po vyřízení citlivé a rychlejší kódování.|Kódování pomocí "H264 s jednou přenosovou rychlostí 1080p" přednastavení trvá přibližně 4 minuty.<br/><br/>Kódování pomocí "H264 Multiple Bitrate 1080p" přednastavení trvá přibližně 8 minut.|

## <a name="considerations"></a>Požadavky

* Analýza zvuku a videa analýzy úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme typ jednotky S3.
* Pokud používáte sdílený fond, to znamená, bez jakékoli rezervovaných jednotek, pak vaše úkoly kódovat mít stejný výkon stejně jako u jednotek ru S1. Však neexistuje žádná horní mez do doby, kdy vaše úlohy můžete využít ve stavu zařazení do fronty, a v daném okamžiku bude spuštěna maximálně pouze jeden úkol.

Zbytek tohoto článku ukazuje, jak používat [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) škálování použité položky.

> [!NOTE]
> Pro úlohy analýzy zvuku a analýzy videa, které jsou aktivované službou Media Services v3 nebo Video Indexerem, důrazně doporučujeme zřídit váš účet s 10 rezervovanými jednotkami S3. Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).
>
> V současné době nelze použít na webu Azure portal ke správě jiných prostředků v3. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](developers-guide.md).

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Škálování médií rezervované jednotky pomocí rozhraní příkazového řádku

Spusťte příkaz `mru`.

Následující [az ams account naposledy použité položky](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) sets rezervované jednotky médií na "amsaccount" účtu pomocí příkazu **počet** a **typ** parametry.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturace

Budou se vám účtovat na počet minut, po které se zřizují rezervovaných jednotek médií ve vašem účtu. K tomu dochází nezávisle na, jestli jsou všechny úlohy spuštěné v účtu. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

* [Kvóty a omezení](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
