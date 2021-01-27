---
title: Použití rozhraní příkazového řádku pro škálování rezervovaných jednotek médií (MRUs) – Azure
description: V tomto tématu se dozvíte, jak pomocí rozhraní příkazového řádku škálovat zpracování médií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 41026a64d6f7549c5a268493cfb5975838002df2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895301"
---
# <a name="how-to-scale-media-reserved-units"></a>Postup škálování rezervovaných jednotek médií

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak škálovat rezervované jednotky médií (MRSs) pro rychlejší kódování.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./create-account-howto.md).

Principy [rezervovaných jednotek médií](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Škálování rezervovaných jednotek médií pomocí rozhraní příkazového řádku

Spusťte příkaz `mru`.

Následující příkaz [AZ AMS Account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) nastaví rezervované jednotky médií na účtu amsaccount pomocí parametrů **Count** a **Type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturace

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu. K tomu dochází nezávisle na tom, zda se ve vašem účtu spouštějí nějaké úlohy. Podrobné vysvětlení najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Viz také

* [Kvóty a omezení](limits-quotas-constraints.md)
