---
title: Připojení řešení Azure VMware k místnímu prostředí
description: Přečtěte si, jak propojit řešení Azure VMware s vaším místním prostředím.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583278"
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

>[!NOTE]
>Každý má jiné prostředí a některé z nich musí umožňovat, aby se tyto trasy rozšířily zpátky do místní sítě.  

Některá prostředí budou mít brány firewall chránící okruh ExpressRoute.  Pokud nejsou k dispozici žádné brány firewall a nedochází k žádnému vyřazování tras, můžete pomocí příkazového testu pro Azure VMware Solution vCenter Server nebo [virtuální počítač](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) v segmentu NSX-T z místního prostředí ověřit.

Kromě toho můžete z virtuálního počítače v segmentu NSX-T získat přístup k prostředkům v místním prostředí.

## <a name="next-steps"></a>Další kroky

Přejděte k další části nasazení a konfigurace nástroje VMware HCX

> [!div class="nextstepaction"]
> [Nasazení a konfigurace HCX VMware](tutorial-deploy-vmware-hcx.md)