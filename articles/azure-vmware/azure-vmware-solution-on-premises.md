---
title: Připojení řešení Azure VMware k místnímu prostředí
description: Přečtěte si, jak propojit řešení Azure VMware s vaším místním prostředím.
ms.topic: tutorial
ms.date: 12/28/2020
ms.openlocfilehash: 753835b0206d8bbabe42b057fa40a2d6c4c8c414
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809679"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Připojení řešení Azure VMware k místnímu prostředí

V tomto článku budete dál používat [informace shromážděné během plánování](production-ready-deployment-steps.md) pro připojení řešení Azure VMware k místnímu prostředí.

Než začnete, existují dva požadavky na připojení řešení Azure VMware k místnímu prostředí:

- Okruh ExpressRoute z místního prostředí do Azure.
- A/29 překrývající se blok síťových adres pro ExpressRoute Global Reach partnerský vztah, který jste definovali v rámci [plánovací fáze](production-ready-deployment-steps.md).

>[!NOTE]
> Můžete se připojit prostřednictvím sítě VPN, ale to je mimo rozsah tohoto dokumentu rychlý Start.

## <a name="establish-an-expressroute-global-reach-connection"></a>Vytvoření připojení ExpressRoute Global Reach

Pokud chcete vytvořit místní připojení k privátnímu cloudu řešení Azure VMware pomocí Global Reach ExpressRoute, postupujte podle pokynů [v místním prostředí pro privátní cloud](tutorial-expressroute-global-reach-private-cloud.md) .

## <a name="verify-on-premises-network-connectivity"></a>Ověření připojení k místní síti

Teď byste měli vidět v místním **hraničním směrovači** , kde ExpressRoute připojuje segmenty sítě NSX-T a segmenty správy řešení Azure VMware.

>[!IMPORTANT]
>Každý má jiné prostředí a některé z nich musí umožňovat, aby se tyto trasy rozšířily zpátky do místní sítě.  

Některá prostředí obsahují brány firewall chránící okruh ExpressRoute.  Pokud nedojde k žádným branám firewall a nedochází k žádnému vyřazování tras, otestujte pomocí příkazového testu Azure VMware Solution vCenter Server nebo [virtuální počítač v segmentu NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) z místního prostředí. Kromě toho můžete z virtuálního počítače v segmentu NSX-T získat přístup k prostředkům v místním prostředí.

## <a name="next-steps"></a>Další kroky

Přejděte k další části nasazení a konfigurace nástroje VMware HCX

> [!div class="nextstepaction"]
> [Nasazení a konfigurace VMware HCX](tutorial-deploy-vmware-hcx.md)