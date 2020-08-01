---
title: Nastavení operací vRealize pro řešení Azure VMware (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476005"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Nastavení operací vRealize pro řešení Azure VMware (AVS)


vRealize Operations Manager je platforma pro správu provozu, která správcům infrastruktury VMware umožňuje monitorovat systémové prostředky. Tyto systémové prostředky můžou být úrovně aplikace nebo úrovně infrastruktury (jak fyzické, tak virtuální) objekty. Historicky většina správců VMware používala operace vRealize k monitorování a správě komponent privátního cloudu VMware – vCenter, ESXi, NSX-T, síti vSAN a hybridního cloudového rozšíření (HCX). Každý privátní cloud pro funkci AVS je zřízen s vyhrazeným nasazením vCenter, NSX-T, síti vSAN a HCX. 

[Před začátkem](#before-you-begin) a [požadavky](#prerequisites) si důkladně prostudujte. Pak vás provedeme dvěma typickými topologiemi nasazení pro vRealize Operations Manager s využitím služby AVS:

> [!div class="checklist"]
> * [Místní operace vRealize spravující nasazení AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Operace vRealize spuštěné při nasazení služby AVS](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Než začnete
* Další informace o nasazení vRealize operací najdete v [dokumentaci k produktu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 
* Projděte si základní [řadu kurzů](tutorial-network-checklist.md)SDDC (software Standarded Software Defined Datacenter).
* V případě potřeby si přečtěte dokumentaci k produktu [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) pro místní operaci vRealize Operations Manage nasazení funkce AVS. 



## <a name="prerequisites"></a>Předpoklady
* VPN nebo Azure ExpressRoute by se měly konfigurovat mezi místními a službou AVS SDDC.
* V Azure je nasazený privátní cloud služby AVS.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Místní operace vRealize spravující nasazení AVS
Většina zákazníků má existující místní nasazení vRealize operací, které slouží ke správě jedné nebo více místních domén servery vCenter. Když zákazníci zřídí nový privátní cloud služby AVS v Azure, obvykle se k místnímu prostředí připojí pomocí služby AVS pomocí Azure ExpressRoute nebo řešení VPN vrstvy 3, jak je znázorněno níže.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Místní operace vRealize spravující nasazení AVS"  border="false":::

K rozšiřování funkcí vRealize operací do privátního cloudu služby AVS můžete vytvořit instanci adaptéru[ pro prostředky privátního cloudu](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) služby AVS – pro shromažďování dat z privátního cloudu služby AVS a jejich přenesení do místních vRealize operací. Místní instance vRealize Operations Manager se může přímo připojit k serveru vCenter a NSX-T Manageru na službě AVS nebo můžete volitelně nasadit vzdálenou kolekci vRealize operací v privátním cloudu AVS. VRealize Operations Remote collector komprimuje a šifruje data shromážděná z privátního cloudu služby AVS předtím, než se pošle přes síť ExpressRoute nebo VPN do vRealize Operations Manager běží místně. 

> [!TIP]
> Podrobného průvodce pro instalaci vRealize Operations Manager najdete v [dokumentaci k VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Operace vRealize spuštěné při nasazení služby AVS

Další možností nasazení je nasadit instanci vRealize Operations Manager v jednom z clusterů vSphere v privátním cloudu služby AVS – jak je znázorněno níže. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operace vRealize běžící na funkci AVS" border="false":::

Po nasazení instance AVS vRealize operací můžete nakonfigurovat operace vRealize pro shromažďování dat z vCenter, ESXi, NSX-T, síti vSAN a HCX. 

> [!TIP]
> Podrobného průvodce pro instalaci vRealize Operations Manager najdete v [dokumentaci k VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .




<!-- LINKS - external -->


<!-- LINKS - internal -->




