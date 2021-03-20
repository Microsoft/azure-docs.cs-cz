---
title: Nastavení operací vRealize pro řešení Azure VMware
description: Naučte se, jak nastavit operace vRealize pro privátní cloud řešení Azure VMware.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 3d2617f893c34fde6a154f9f93b5bda520c5a91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738425"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Nastavení operací vRealize pro řešení Azure VMware


vRealize Operations Manager je platforma pro správu provozu, která správcům infrastruktury VMware umožňuje monitorovat systémové prostředky. Tyto systémové prostředky můžou být úrovně aplikace nebo úrovně infrastruktury (jak fyzické, tak virtuální) objekty. Většina správců VMware používala operace vRealize k monitorování a správě komponent privátního cloudu VMware – vCenter, ESXi, NSX-T, síti vSAN a VMware HCX.  Každý zřízený privátní cloud řešení Azure VMware zahrnuje vyhrazené nasazení vCenter, NSX-T, síti vSAN a HCX. 

[Před začátkem](#before-you-begin) a [požadavky](#prerequisites) si důkladně prostudujte. Pak vás provedeme dvěma typickými topologiemi nasazení:

> [!div class="checklist"]
> * [Místní operace vRealize spravující nasazení řešení Azure VMware](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operace vRealize běžící na nasazení řešení Azure VMware](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Než začnete
* Další informace o nasazení operací vRealize najdete v [dokumentaci k vRealize Operations Manager produktu](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 
* Přečtěte si základní [řadu kurzů](tutorial-network-checklist.md)řešení Azure VMware Software-Defined DATACENTER (SDDC).
* Volitelně si přečtěte dokumentaci k produktu [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) pro místní operace vRealize, které spravují možnost nasazení řešení Azure VMware. 


## <a name="prerequisites"></a>Předpoklady
* je nainstalovaná [Operations Manager vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .
* SÍŤ VPN nebo Azure ExpressRoute konfigurovaná mezi místními prostředími a Azure VMware Solution SDDC.
* V Azure je nasazený privátní cloud řešení Azure VMware.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Místní operace vRealize spravující nasazení řešení Azure VMware
Většina zákazníků má stávající místní nasazení operací vRealize pro správu jedné nebo několika místních domén servery vCenter. Když zřídí privátní cloud řešení Azure VMware, připojí své místní prostředí ke svému privátnímu cloudu s využitím řešení Azure ExpressRoute nebo VPN úrovně 3.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Místní operace vRealize spravující nasazení řešení Azure VMware" border="false":::

K rozšiřování funkcí vRealize operací do privátního cloudu řešení Azure VMware vytvoříte [instanci adaptéru pro prostředky privátního cloudu](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Shromažďuje data z privátního cloudu řešení Azure VMware a přináší je do místních vRealize operací. Místní instance vRealize Operations Manager se může přímo připojit ke Správci vCenter a NSX-T Manageru v řešení Azure VMware. Volitelně můžete nasadit vzdálené kolektor operací vRealize do privátního cloudu řešení Azure VMware. Kolektor komprimuje a šifruje data shromážděná z privátního cloudu předtím, než se pošle přes síť ExpressRoute nebo VPN, do vRealize Operations Manager spuštěná v místním počítači. 

> [!TIP]
> Podrobného průvodce pro instalaci vRealize Operations Manager najdete v [dokumentaci k VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operace vRealize běžící na nasazení řešení Azure VMware

Další možností je nasadit instanci vRealize Operations Manager v clusteru vSphere v privátním cloudu. 

>[!IMPORTANT]
>Tato možnost v současnosti není podporována VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operace vRealize běžící na řešení VMware Azure" border="false":::

Po nasazení instance můžete nakonfigurovat operace vRealize pro shromažďování dat z vCenter, ESXi, NSX-T, síti vSAN a HCX. 



## <a name="known-limitations"></a>Známá omezení

- **Cloudadmin \@ vSphere. místní** uživatel v řešení Azure VMware má [omezená oprávnění](concepts-role-based-access-control.md).  Virtuální počítače v řešení Azure VMware nepodporují shromažďování paměti hosta pomocí nástrojů VMware.  Využití aktivní a spotřebované paměti bude v tomto případě nadále fungovat.
- Optimalizace úloh pro obchodní záměr založený na hostiteli nefunguje, protože řešení Azure VMware spravují konfigurace clusteru, včetně nastavení DRS.
- Optimalizace zatížení pro umístění mezi clustery v rámci SDDC pomocí podnikového záměru založeného na clusteru je plně podporovaná s vRealize Operations Manager 8,0 a vyššími. Optimalizace úloh ale neví o fondech zdrojů a umístí virtuální počítače na úrovni clusteru. Uživatel ji může ručně opravit v rozhraní řešení Azure VMware vCenter Server.
- K vRealize Operations Manager se nemůžete přihlásit pomocí vCenter Server přihlašovacích údajů Azure VMware. 
- Řešení Azure VMware nepodporuje modul plug-in vRealize Operations Manager.

Když připojíte Azure VMware Solution vCenter k vRealize Operations Manager pomocí cloudového účtu vCenter Server, zobrazí se upozornění:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Vytvoření instance upozornění se podařilo.":::

K tomuto upozornění dochází, protože **cloudadmin \@ vSphere. místní** uživatel v řešení VMware VMware nemá dostatečná oprávnění k provedení všech akcí vCenter Server požadovaných k registraci. Tato oprávnění jsou nicméně dostačující pro instance adaptéru k shromažďování dat, jak je vidět níže:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Instance adaptéru pro provedení shromažďování dat":::

Další informace najdete v tématu [oprávnění požadovaná pro konfiguraci instance adaptéru vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




