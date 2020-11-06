---
title: Kurz – přidání síťového segmentu NSX-T do řešení Azure VMware
description: Naučte se vytvářet segment sítě NSX-T, který se má používat pro virtuální počítače (VM) v vCenter.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335042"
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
