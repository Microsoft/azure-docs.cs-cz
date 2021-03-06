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
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121672"
---
# <a name="how-to-scale-media-reserved-units"></a>Postup škálování rezervovaných jednotek médií

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak škálovat rezervované jednotky médií (MRSs) pro rychlejší kódování.

> [!WARNING]
> Tento příkaz už nebude fungovat pro účty Media Services, které jsou vytvořené pomocí verze 2020-05-01 rozhraní API nebo novějšího. Pro tyto účty už nejsou potřebné rezervované jednotky médií, protože systém bude automaticky škálovat nahoru a dolů na základě zatížení. Pokud nevidíte možnost spravovat MRUs v Azure Portal, používáte účet, který byl vytvořen s rozhraním API 2020-05-01 nebo novějším.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./account-create-how-to.md).

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

[Analýza videí](analyze-videos-tutorial.md)

## <a name="see-also"></a>Viz také

* [Kvóty a omezení](limits-quotas-constraints-reference.md)
