---
title: Zobrazení, aktualizace a odstranění prostředků obrázků pomocí Azure CLI
description: Vypíše, aktualizuje a odstraní prostředky obrázků v galerii sdílených imagí pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3554eccf28c5abbe9741af5b9718fb83710dab38
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553625"
---
# <a name="list-update-and-delete-image-resources"></a>Vypsat, aktualizovat a odstranit prostředky obrázků 

Prostředky galerie sdílených imagí můžete spravovat pomocí rozhraní příkazového řádku Azure CLI.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Další kroky

[Azure image Builder (Preview)](./image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](./linux/image-builder-gallery-update-image-version.md).