---
title: Připojit virtuální počítače Windows v cloudové službě | Microsoft Docs
description: Připojte virtuální počítače s Windows, které jsou vytvořené pomocí modelu nasazení classic do cloudové služby Azure nebo virtuální sítě.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: b5778336b2dfb3d65cb678c96525ec22da1634a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917754"
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Připojit virtuální počítače s Windows, které jsou vytvořené pomocí modelu nasazení classic pomocí virtuální sítě nebo cloudové služby
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Virtuální počítače s Windows, které jsou vytvořené pomocí modelu nasazení classic jsou vždy umístěny v cloudové službě. Cloudové služby slouží jako kontejner a poskytuje jedinečný veřejný název DNS, veřejnou IP adresu a sada koncových bodů pro přístup k virtuálnímu počítači přes Internet. Cloudové služby může být ve virtuální síti, ale není povinné.

Pokud cloudové služby není ve virtuální síti, se nazývá *samostatné* Cloudová služba. Virtuální počítače v cloudové službě samostatné komunikovat s jinými virtuálními počítači pomocí veřejné názvy DNS jinými virtuálními počítači a provoz se přenáší přes Internet. Pokud cloudové služby ve virtuální síti, virtuální počítače v dané cloudové služby může komunikovat s všechny ostatní virtuální počítače ve virtuální síti bez odeslání veškerou komunikaci přes Internet.

Pokud jste v rámci stejné cloudové služby samostatné virtuální počítače, můžete nadále používat vyrovnávání zatížení a skupiny dostupnosti. Podrobnosti najdete v tématu [virtuální počítače Vyrovnávání zatížení](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [Správa dostupnosti virtuálních počítačů](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Nelze však uspořádat virtuální počítače v podsítích nebo připojení k síti na pracovišti samostatné cloudové služby. Tady je příklad:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření virtuálního počítače, je vhodné [přidat datový disk](attach-disk.md) aby umístění pro uložení dat služby a úlohy.
