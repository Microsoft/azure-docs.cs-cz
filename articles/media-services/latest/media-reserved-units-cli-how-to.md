---
title: Škálování média rezervované jednotky – Azure | Dokumentace Microsoftu
description: V tomto tématu je uveden přehled škálování zpracování médií pomocí Azure Media Services.
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: dd587e5fc2082d1e496fbc05d5b25cf6692413bc
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713057"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Azure Media Services umožňuje škálování zpracování médií ve svém účtu tím, že spravuje rezervované jednotky médií (použité položky). Podrobný přehled najdete v tématu [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

Tento článek popisuje, jak používat [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) škálování použité položky.

> [!NOTE]
> Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. <br/>Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky 

- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

- [Vytvoření účtu Media Services](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Škálování médií rezervované jednotky pomocí rozhraní příkazového řádku

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
