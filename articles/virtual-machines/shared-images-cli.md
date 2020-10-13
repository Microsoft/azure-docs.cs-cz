---
title: Vytváření galerií sdílených imagí pomocí Azure CLI
description: V tomto článku se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit sdílenou bitovou kopii virtuálního počítače v Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff50f61fe159c518d488da9f9911e40f1ddb8ca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501453"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Vytvoření galerie sdílených imagí pomocí Azure CLI

[Galerie sdílených imagí](./linux/shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet vlastní image virtuálních počítačů s ostatními. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Další kroky

Vytvořte verzi image z [virtuálního počítače](image-version-vm-cli.md)nebo [spravované image](image-version-managed-image-cli.md) pomocí Azure CLI.

Další informace o galeriích sdílených imagí najdete v [přehledu](./linux/shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](troubleshooting-shared-images.md).
