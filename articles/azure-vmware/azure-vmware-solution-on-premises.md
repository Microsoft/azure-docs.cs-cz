---
title: Připojení řešení Azure VMware k místnímu prostředí
description: Přečtěte si, jak propojit řešení Azure VMware s vaším místním prostředím.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491788"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Připojení řešení Azure VMware k místnímu prostředí

V tomto článku budete dál používat [informace shromážděné během plánování](production-ready-deployment-steps.md) pro připojení řešení Azure VMware k místnímu prostředí.

Než začnete, existují dva požadavky na připojení řešení Azure VMware k místnímu prostředí:

- Okruh ExpressRoute z místního prostředí do Azure.
- A/29 překrývající se blok síťových adres CIDR pro ExpressRoute Global Reach partnerský vztah, který jste definovali v rámci [plánovací fáze](production-ready-deployment-steps.md).

>[!NOTE]
> Můžete se připojit prostřednictvím sítě VPN, ale to je mimo rozsah tohoto dokumentu rychlý Start.

## <a name="establish-an-expressroute-global-reach-connection"></a>Vytvoření připojení ExpressRoute Global Reach

Pokud chcete vytvořit místní připojení k privátnímu cloudu řešení Azure VMware pomocí Global Reach ExpressRoute, postupujte podle pokynů [v místním prostředí pro privátní cloud](tutorial-expressroute-global-reach-private-cloud.md) .

Výsledkem tohoto kurzu je připojení, jak je znázorněno v diagramu.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach diagram připojení k místní síti." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Ověření připojení k místní síti

Teď byste měli vidět v místním **hraničním směrovači** , kde ExpressRoute připojuje segmenty sítě NSX-T a segmenty správy řešení Azure VMware.

>[!IMPORTANT]
>Každý má jiné prostředí a některé z nich musí umožňovat, aby se tyto trasy rozšířily zpátky do místní sítě.  

Některá prostředí obsahují brány firewall chránící okruh ExpressRoute.  Pokud nedojde k žádným branám firewall a nedochází k žádnému vyřazování tras, otestujte pomocí příkazového testu Azure VMware Solution vCenter Server nebo [virtuální počítač v segmentu NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) z místního prostředí. Kromě toho můžete z virtuálního počítače v segmentu NSX-T získat přístup k prostředkům v místním prostředí.

## <a name="next-steps"></a>Další kroky

Přejděte k další části nasazení a konfigurace nástroje VMware HCX

> [!div class="nextstepaction"]
> [Nasazení a konfigurace VMware HCX](tutorial-deploy-vmware-hcx.md)