---
title: Přidat Linuxové Image do služby Azure Stack
description: Zjistěte, jak přidat Linuxové Image do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: be537d9598eeda902254e56a9202415f4f498d93
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245129"
---
# <a name="add-linux-images-to-azure-stack"></a>Přidat Linuxové Image do služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Přidáním bitové kopie založené na Linuxu v Tržišti Azure Stack můžete nasadit virtuální počítače s Linuxem (VM) ve službě Azure Stack. Nejjednodušší způsob, jak přidat do image Linuxu do služby Azure Stack je prostřednictvím webu Marketplace správu. Tyto bitové kopie byly připravit a otestovat jejich kompatibilitu s Azure Stack.

## <a name="marketplace-management"></a>Správa webu Marketplace

Stažení imagí Linuxu na Azure Marketplace, pomocí postupů na portále [stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md) článku. Vyberte Image Linuxu, které mají uživatelé ve vaší službě Azure Stack. 

Všimněte si, že jsou časté aktualizace do těchto bitových kopií, tak správu Marketplace často se má aktualizovat.

## <a name="prepare-your-own-image"></a>Příprava svoji vlastní image

Kdykoli je to možné, stáhněte si Image, která je dostupná prostřednictvím webu Marketplace správu, který byl předem připraven a testovat pro službu Azure Stack. 
 
Azure Linux Agent (obvykle nazývá `WALinuxAgent` nebo `walinuxagent`) je nutné použít, a ne všechny verze agenta bude fungovat ve službě Azure Stack. Používejte verze 2.2.18 nebo novější, pokud vytvoříte vlastní image. Všimněte si, že [cloud-init](https://cloud-init.io/) není v tuto chvíli nepodporuje v Azure stacku.

Můžete připravit své vlastní image s Linuxem podle následujících pokynů:

* [Distribuce založené na centOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Přidání bitové kopie na webu Marketplace
 
Postupujte podle [Přidání bitové kopie na webu Marketplace](azure-stack-add-vm-image.md). Ujistěte se, že `OSType` parametr je nastaven na `Linux`.

Po přidání obrázku na webu Marketplace, je vytvořena položka Marketplace a uživatelé můžou nasazovat virtuální počítače s Linuxem.

## <a name="next-steps"></a>Další postup

Zobrazit další informace v následujících článcích:

- [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Přehled služby Azure Stack Marketplace](azure-stack-marketplace.md)