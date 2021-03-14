---
title: Kurz – přidání síťového segmentu NSX-T do řešení Azure VMware
description: Naučte se vytvářet segment sítě NSX-T, který se má používat pro virtuální počítače (VM) v vCenter.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462115"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Kurz: přidání segmentu sítě v řešení Azure VMware 

Virtuální počítače vytvořené v vCenter jsou umístěné do segmentů sítě vytvořených v NSX-T a jsou viditelné v vCenter.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pokud chcete přidat segmenty sítě, přejděte do správce NSX-T.
> * Přidat nový segment sítě
> * Sledovat nový segment sítě v vCenter

## <a name="prerequisites"></a>Požadavky

Privátní cloud řešení Azure VMware s přístupem k rozhraním vCenter a NSX-T Manageru. Další informace najdete v kurzu [Konfigurace sítě](tutorial-configure-networking.md) .

## <a name="add-a-network-segment"></a>Přidat segment sítě

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili segment sítě NSX-T pro použití pro virtuální počítače v vCenter. 

Teď můžete: 

- [Vytvoření a Správa DHCP pro řešení Azure VMware](manage-dhcp.md)
- [Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware](deploy-vm-content-library.md) 
- [Partnerských místních prostředí do privátního cloudu](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
