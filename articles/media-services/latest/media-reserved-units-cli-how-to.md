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
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b40ab6bcc2f718eda85ff64d69a6689e12d60ab8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094831"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Azure Media Services umožňuje škálování zpracování médií ve svém účtu tím, že spravuje rezervované jednotky médií (použité položky). Podrobný přehled najdete v tématu [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

Tento článek popisuje, jak používat [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) škálování použité položky.

> [!NOTE]
> Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. <br/>Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

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

Budou se vám účtovat na číslo, typu a množství času, které jsou zřízené použité položky ve vašem účtu. Poplatky za zda spuštěním jakékoli úlohy. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
