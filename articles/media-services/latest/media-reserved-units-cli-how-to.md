---
title: Škálování rezervovaných jednotek médií (MRUs) CLI
description: V tomto tématu se dozvíte, jak pomocí rozhraní příkazového řádku škálovat zpracování médií pomocí Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870156"
---
# <a name="how-to-scale-media-reserved-units"></a>Postup škálování rezervovaných jednotek médií

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak škálovat rezervované jednotky médií (MRSs) pro rychlejší kódování.

> [!WARNING]
> Tento příkaz už nebude fungovat pro účty Media Services, které jsou vytvořené pomocí verze 2020-05-01 rozhraní API nebo novějšího. Pro tyto účty už nejsou potřebné rezervované jednotky médií, protože systém bude automaticky škálovat nahoru a dolů na základě zatížení. Pokud nevidíte možnost spravovat MRUs v Azure Portal, používáte účet, který byl vytvořen s rozhraním API 2020-05-01 nebo novějším.

## <a name="prerequisites"></a>Předpoklady

[Vytvořte účet Media Services](./create-account-howto.md).

Principy [rezervovaných jednotek médií](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Škálování rezervovaných jednotek médií pomocí rozhraní příkazového řádku

Spusťte příkaz `mru`.

Následující příkaz [AZ AMS Account MRU](/cli/azure/ams/account/mru) nastaví rezervované jednotky médií na účtu amsaccount pomocí parametrů **Count** a **Type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturace

Účtují se vám poplatky podle počtu minut, po které jsou rezervované jednotky médií zřízené ve vašem účtu. K tomu dochází nezávisle na tom, zda se ve vašem účtu spouštějí nějaké úlohy. Podrobné vysvětlení najdete v části Nejčastější dotazy stránky s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Viz také

* [Kvóty a omezení](limits-quotas-constraints.md)
