---
title: Použití sdílených imagí virtuálních počítačů k vytvoření sady škálování v Azure CLI
description: Naučte se používat rozhraní příkazového řádku Azure CLI k vytváření sdílených imagí virtuálních počítačů, které se použijí pro nasazení služby Virtual Machine Scale Sets v Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: c056db21eea2d87471c80ea7c4811eed418fcbd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323460"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Vytvoření a použití sdílených imagí pro Virtual Machine Scale Sets pomocí Azure CLI 2,0

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. [Galerie sdílených imagí](shared-image-galleries.md) zjednodušuje sdílení vlastních imagí v rámci vaší organizace. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených imagí umožňuje sdílet obrázky s ostatními. Vyberte, které Image chcete sdílet, které oblasti mají být v nástroji dostupné a které chcete sdílet s. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Další kroky

Vytvořte verzi image z [virtuálního počítače](../virtual-machines/image-version-vm-cli.md)nebo [spravované image](../virtual-machines/image-version-managed-image-cli.md).

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](../virtual-machines/troubleshooting-shared-images.md).
