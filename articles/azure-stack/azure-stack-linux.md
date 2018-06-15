---
title: Přidání bitové kopie systému Linux do Azure zásobníku
description: Zjistěte, jak přidat Linux bitové kopie do protokolů Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935704"
---
# <a name="add-linux-images-to-azure-stack"></a>Přidání bitové kopie systému Linux do Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Linuxové virtuální počítače (VM) můžete nasadit v Azure zásobníku přidáním bitové kopie založené na systému Linux do Azure Marketplace zásobníku. Nejjednodušší způsob, jak přidat bitovou kopii systému Linux do protokolů Azure je přes správu Marketplace. Tyto bitové kopie byly připravené a testovány z hlediska kompatibility s Azure zásobníku.

## <a name="marketplace-management"></a>Správa webu Marketplace

Chcete-li Linux bitové kopie z Azure Marketplace, použijte postupy v následujícím článku. Vyberte Image Linux, které můžete chtít nabízet uživatelům v zásobníku vaší Azure. 

[Stažení položky marketplace z Azure do Azure zásobníku](azure-stack-download-azure-marketplace-item.md).

Všimněte si, že jsou časté aktualizace do těchto bitových kopií, proto Marketplace Management často k zachování aktualizovaného stavu.

## <a name="prepare-your-own-image"></a>Příprava vlastní image

 Pokud je to možné, stažení bitové kopie, které přes správu Marketplace, které byly připravené a testována zásobník Azure. 
 
 Azure Linux Agent (obvykle nazývá `WALinuxAgent` nebo `walinuxagent`) je nutné, a ne všechny verze agenta bude fungovat v Azure zásobníku. Měli byste použít verzi 2.2.18 nebo novější, pokud vytvoříte vlastní image. Všimněte si, že [cloudu init](https://cloud-init.io/) v tuto chvíli není podporován v zásobníku Azure.

 Vlastní image Linux pomocí následujících pokynů můžete připravit:

   * [Na základě centOS distribuce](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Přidání bitové kopie do marketplace.
 
Postupujte podle [lze přidat image do Marketplace](azure-stack-add-vm-image.md). Ujistěte se, že `OSType` parametr je nastaven na `Linux`.

Po přidání bitovou kopii na Marketplace, je-li vytvořit položku Marketplace. a mohou uživatelé nasadit virtuální počítač s Linuxem.
