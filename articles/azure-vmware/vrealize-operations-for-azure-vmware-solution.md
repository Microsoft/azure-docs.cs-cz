---
title: Nastavení operací vRealize pro řešení Azure VMware
description: Naučte se, jak nastavit operace vRealize pro privátní cloud řešení Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579942"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Nastavení operací vRealize pro řešení Azure VMware


vRealize Operations Manager je platforma pro správu provozu, která správcům infrastruktury VMware umožňuje monitorovat systémové prostředky. Tyto systémové prostředky můžou být úrovně aplikace nebo úrovně infrastruktury (jak fyzické, tak virtuální) objekty. Historicky většina správců VMware používala operace vRealize k monitorování a správě komponent privátního cloudu VMware – vCenter, ESXi, NSX-T, síti vSAN a hybridního cloudového rozšíření (HCX). U každého privátního cloudu řešení Azure VMware se zřizuje vyhrazené nasazení vCenter, NSX-T, síti vSAN a HCX. 

[Před začátkem](#before-you-begin) a [požadavky](#prerequisites) si důkladně prostudujte. Pak vás provedeme dvěma typickými topologiemi nasazení pro vRealize Operations Manager s využitím řešení Azure VMware:

> [!div class="checklist"]
> * [Místní operace vRealize spravující nasazení řešení Azure VMware](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operace vRealize běžící na nasazení řešení Azure VMware](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Než začnete
* Další informace o nasazení vRealize operací najdete v [dokumentaci k produktu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 
* Přečtěte si základní [řadu kurzů](tutorial-network-checklist.md)pro Azure VMware Solution software definované DATACENTER (SDDC).
* Volitelně si přečtěte dokumentaci k produktu [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) pro místní operace vRealize, které spravují možnost nasazení řešení Azure VMware. 



## <a name="prerequisites"></a>Předpoklady
* VPN nebo Azure ExpressRoute by se měly nakonfigurovat mezi místními a Azure VMware Solution SDDC.
* V Azure je nasazený privátní cloud řešení Azure VMware.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Místní operace vRealize spravující nasazení řešení Azure VMware
Většina zákazníků má existující místní nasazení vRealize operací, které slouží ke správě jedné nebo více místních domén servery vCenter. Když si zákazníci zřídí nový privátní cloud řešení Azure VMware v Azure, obvykle se k řešení Azure VMware připojuje pomocí Azure ExpressRoute nebo řešení VPN vrstvy 3, jak je znázorněno níže.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Místní operace vRealize spravující nasazení řešení Azure VMware"  border="false":::

K rozšiřování funkcí vRealize operací do privátního cloudu řešení Azure VMware je možné vytvořit instanci adaptéru [pro prostředky privátního cloudu řešení Azure VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) – ke shromáždění dat z privátního cloudu řešení Azure VMware a jejich přenesení do místních vRealize operací. Místní instance vRealize Operations Manager se může přímo připojit ke Správci vCenter a NSX-T ve službě Azure VMware a případně můžete nasadit vzdálenou kolekci vRealize operací v privátním cloudu řešení Azure VMware. VRealize operace vzdáleného kolektoru komprimuje a šifruje data shromážděná z privátního cloudu řešení Azure VMware předtím, než se pošle přes síť ExpressRoute nebo VPN do vRealize Operations Manager spuštěná v místním prostředí. 

> [!TIP]
> Podrobného průvodce pro instalaci vRealize Operations Manager najdete v [dokumentaci k VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operace vRealize běžící na nasazení řešení Azure VMware

Další možností nasazení je nasadit instanci vRealize Operations Manager v jednom z clusterů vSphere v privátním cloudu řešení Azure VMware – jak je znázorněno níže. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Místní operace vRealize spravující nasazení řešení Azure VMware" border="false":::

Po nasazení instance řešení Azure VMware pro vRealize operace můžete nakonfigurovat operace vRealize pro shromažďování dat z vCenter, ESXi, NSX-T, síti vSAN a HCX. 

> [!TIP]
> Podrobného průvodce pro instalaci vRealize Operations Manager najdete v [dokumentaci k VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .


## <a name="known-limitations"></a>Známá omezení

- **cloudadmin@vsphere.local**Uživatel v řešení Azure VMware má [omezená oprávnění](concepts-role-based-access-control.md). Shromažďování paměti v hostovaném prostředí pomocí nástrojů VMware není podporováno u virtuálních počítačů v řešení Azure VMware. V tomto případě budou v tomto případě nadále fungovat aktivní a spotřebovaná využití paměti.
- Optimalizace úloh pro obchodní záměr založený na hostiteli nefunguje, protože řešení Azure VMware spravují konfigurace clusteru, včetně nastavení DRS.
- Optimalizace zatížení pro umístění mezi clustery v rámci SDDC pomocí podnikového záměru založeného na clusteru je plně podporovaná s vRealize Operations Manager 8,0 a vyššími. Optimalizace úloh ale neví o fondech zdrojů a umísťuje virtuální počítače na úrovni clusteru. Uživatel ho může ručně opravit v rozhraní Azure VMware Solution vCenter Server.
- K vRealize Operations Manager se nemůžete přihlásit pomocí vCenter Server přihlašovacích údajů k řešení Azure VMware. 
- Řešení Azure VMware nepodporuje modul plug-in vRealize Operations Manager.

Při připojení Azure VMware Solution vCenter k vRealize Operations Manager pomocí vCenter Server cloudového účtu se zobrazí následující upozornění:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Místní operace vRealize spravující nasazení řešení Azure VMware":::

K tomuto upozornění dochází, protože **cloudadmin@vsphere.local** uživatel v řešení Azure VMware nemá dostatečná oprávnění, aby mohl provádět všechny vCenter Server akce vyžadované k registraci. Tato oprávnění jsou nicméně dostačující pro instance adaptéru k shromažďování dat, jak je vidět níže:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Místní operace vRealize spravující nasazení řešení Azure VMware":::

Další informace najdete v tématu [oprávnění požadovaná pro konfiguraci instance adaptéru vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




