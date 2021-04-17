---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529569"
---
> [!NOTE]
> Azure poskytuje dočasný protokol IP pro Azure Virtual Machines, kterým není přiřazená veřejná IP adresa, nebo se nachází v back-endu vnitřního základního Azure Load Balancer. Dočasný mechanismus IP adres poskytuje odchozí IP adresu, kterou není možné konfigurovat. 
>
>Pokud se k virtuálnímu počítači přiřadí veřejná IP adresa nebo se virtuální počítač umístí do back-endu Standard Load Balancer s odchozími pravidly, je dočasný protokol IP zakázán. Pokud je prostředek brány [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) přiřazený k podsíti virtuálního počítače, je tato dočasný IP adresa zakázaná.
>
> Další informace o odchozích připojeních v Azure najdete v tématu [použití překladu zdrojového síťového adres (SNAT) pro odchozí připojení](../articles/load-balancer/load-balancer-outbound-connections.md).