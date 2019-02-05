---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 127ade3b37380c50461c5b648265b431a2f4467e
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735778"
---
*Vlastní* virtuální počítač znamená jednoduše virtuální počítač, který vytvoříte pomocí **vybrané aplikace** z **Marketplace**, protože za vás udělá velkou část práce. I tak ale můžete upravovat konfiguraci včetně následujících položek:

* Připojení virtuálního počítače k virtuální síti.
* Instalace agenta virtuálního počítače Azure a rozšíření virtuálního počítače Azure, například pro antimalware.
* Přidání virtuálního počítače do existujících cloudových služeb.
* Přidání virtuálního počítače do existujícího účtu úložiště.
* Přidání virtuálního počítače do skupiny dostupnosti.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Pokud chcete, aby virtuální počítač používal virtuální síť, ujistěte se, že je virtuální síť určena při vytváření virtuálního počítače.

> * Dvě výhody používání virtuální sítě představují připojení přímo k virtuálnímu počítači a nastavení připojení mezi různými místy.

> * Virtuální počítač je možné konfigurovat tak, aby se k virtuální síti připojil jen tehdy, když virtuální počítač vytvoříte. Podrobnosti týkající se virtuálních sítí najdete v tématu [Přehled Azure Virtual Network](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>Vytvoření virtuálního počítače
