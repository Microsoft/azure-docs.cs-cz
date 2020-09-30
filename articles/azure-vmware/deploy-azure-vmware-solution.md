---
title: Nasazení a konfigurace řešení Azure VMware
description: Naučte se používat informace shromážděné ve fázi plánování k nasazení privátního cloudu řešení Azure VMware.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 08d2d48820505dad9bba74fe3ac84f45525f4525
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583281"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Nasazení a konfigurace řešení Azure VMware

V tomto článku použijete informace z [části plánování](production-ready-deployment-steps.md) k nasazení řešení Azure VMware. Pokud jste informace nedefinovali, vraťte se zpět do [části plánování](production-ready-deployment-steps.md) a teprve potom pokračujte.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Nasazení řešení Azure VMware

Použijte informace, které jste shromáždili v článku [Plánování nasazení řešení Azure VMware](production-ready-deployment-steps.md) :

>[!NOTE]
>K nasazení řešení Azure VMware je nutné mít v předplatném minimální úroveň přispěvatele.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]



## <a name="create-the-jump-box"></a>Vytvoření pole pro skok

>[!IMPORTANT]
>Pokud jste při počátečním kroku zřizování na obrazovce **Vytvoření privátního cloudu** opustili možnost **Virtual Network** , **před** pokračováním v této části dokončete kurz [Konfigurace sítě pro privátní cloud VMware](tutorial-configure-networking.md) .  



Po nasazení řešení Azure VMware vytvoříte pole pro skok virtuální sítě, které se připojí k vCenter a NSX. Jakmile nakonfigurujete okruhy ExpressRoute a ExpressRoute Global Reach, není pole s odkazem nutné.  Je ale užitečné dosáhnout vCenter a NSX v řešení Azure VMware.  


:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Vytvoření pole pro přechod řešení Azure VMware":::

Při vytváření virtuálního počítače ve virtuální síti, kterou jste [identifikovali nebo vytvořili v rámci procesu nasazení](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution), postupujte podle těchto pokynů: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Připojení k virtuální síti pomocí ExpressRoute

Pokud jste v kroku nasazení nedefinovali virtuální síť a vaším záměrem je připojit ExpressRoute řešení Azure VMware k existující bráně ExpressRoute, postupujte podle následujících kroků.

Pokud jste už virtuální síť definovali na obrazovce nasazení v Azure, přejděte k další části.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Ověřit inzerované síťové trasy

Pole s odkazem se nachází ve virtuální síti, ve které se řešení Azure VMware připojuje přes svůj okruh ExpressRoute.  V Azure přejděte do síťového rozhraní pro skok a [Podívejte se na efektivní trasy](../virtual-network/manage-route-table.md#view-effective-routes).

V seznamu efektivní trasy by se měly zobrazit sítě vytvořené jako součást nasazení řešení Azure VMware. Uvidíte několik sítí, které byly odvozeny od [ `/22` sítě, kterou jste definovali](production-ready-deployment-steps.md#ip-address-segment) během [postupu nasazení](#deploy-azure-vmware-solution) dříve v tomto článku.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Vytvoření pole pro přechod řešení Azure VMware":::

V tomto příkladu byla během nasazení v síti 10.74.72.0/22 zadaná síť, která je odvozená od/24 sítí.  Pokud vidíte něco podobného, můžete se připojit k vCenter v řešení Azure VMware.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Připojení a přihlášení k vCenter a NSX-T

Přihlaste se k poli odkazů, které jste vytvořili v předchozím kroku. Po přihlášení otevřete webový prohlížeč a přejděte do konzoly pro správu vCenter a NSX-T a přihlaste se k ní.  

IP adresy a přihlašovací údaje konzoly pro správu NSX-T můžete identifikovat v Azure Portal.  Vyberte privátní cloud a potom v zobrazení **Přehled** vyberte **Identita > výchozí**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Vytvoření segmentu sítě v řešení VMware Azure

Pomocí NSX-T můžete vytvořit nové segmenty sítě ve vašem prostředí řešení Azure VMware.  V [části plánování](production-ready-deployment-steps.md)jste definovali sítě, které chcete vytvořit.  Pokud jste je nedefinovali, vraťte se zpět do [části plánování](production-ready-deployment-steps.md) , než budete pokračovat.

>[!IMPORTANT]
>Ujistěte se, že blok síťových adres CIDR, který jste definovali, se nepřekrývá s cokoli v Azure nebo v místním prostředí.  

Postup vytvoření segmentu sítě NSX-T v řešení Azure VMware najdete v kurzu [Vytvoření segmentu sítě NSX-t v rámci Azure VMware Solution](tutorial-nsx-t-network-segment.md) .

## <a name="verify-advertised-nsx-t-segment"></a>Ověřit segment inzerované NSX-T

Vraťte se zpět na krok [ověřit inzerovanou síťovou trasu](#verify-network-routes-advertised) . V seznamu se zobrazí další trasy, které představují segmenty sítě, které jste vytvořili v předchozím kroku.  

Pro virtuální počítače přiřadíte segmenty, které jste vytvořili v kroku [ověření inzerované NSX-T segmentu](#verify-advertised-nsx-t-segment) .  

Vzhledem k tomu, že se vyžaduje DNS, určete, jaký server DNS chcete použít.  

- Pokud máte nakonfigurované Global Reach ExpressRoute, použijte libovolný server DNS, který byste použili místně.  
- Pokud máte server DNS v Azure, použijte ho.  
- Pokud ani jednu z nich nemáte, použijte libovolný server DNS, který vaše pole s odkazem používá.

>[!NOTE]
>V tomto kroku identifikujete server DNS a v tomto kroku neprovedete žádné konfigurace.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Volitelné Poskytněte služby DHCP NSX síťovému segmentu-T.

Pokud máte v úmyslu používat na segmentech NSX-T protokol DHCP, pokračujte v této části. V opačném případě přejděte na oddíl [Přidat virtuální počítač v části segment sítě NSX-T](#add-a-vm-on-the-nsx-t-network-segment) .  

Teď, když jste vytvořili segment sítě NSX-T, můžete provést jeden z následujících způsobů:

* Jako server DHCP pro vytvořené segmenty použijte NSX-T. Pro tuto možnost budete chtít [vytvořit server DHCP v NSX-T](manage-dhcp.md#create-dhcp-server) a [předat ho k tomuto serveru](manage-dhcp.md#create-dhcp-relay-service).
* Přenášet požadavky DHCP od segmentů NSX-T na server DHCP jinde ve vašem prostředí. Pro tuto možnost [proveďte pouze konfiguraci přenosu](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Přidání virtuálního počítače do segmentu sítě NSX-T

V Azure VMware Solution vCenter nasaďte virtuální počítač a použijte ho k ověření připojení ze sítě řešení Azure VMware do:

- Síť Internet
- Virtuální sítě Azure
- Místně.  

Virtuální počítač nasaďte stejně jako v jakémkoli prostředí vSphere.  Připojte virtuální počítač k jednomu z segmentů sítě, které jste dříve vytvořili v NSX-T.  

>[!NOTE]
>Pokud nastavíte server DHCP, získáte konfiguraci sítě pro virtuální počítač (Nezapomeňte nastavit obor).  Pokud budete konfigurovat staticky, nakonfigurujte jako obvykle.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testování připojení segmentů NSX-T

Přihlaste se k virtuálnímu počítači vytvořenému v předchozím kroku a ověřte připojení.

1. Proveďte na internetu test na IP adresu.
2. Přejít na web Internet přes webový prohlížeč.
3. Otestujte pole s odkazem, které se nachází na Virtual Network Azure.

>[!IMPORTANT]
>V tomto okamžiku je řešení Azure VMware spuštěné a běžící a úspěšně jste navázali připojení k a z Azure Virtual Network a Internetu.

## <a name="next-steps"></a>Další kroky

V další části připojíte řešení Azure VMware k místní síti přes ExpressRoute.
> [!div class="nextstepaction"]
> [Připojení řešení Azure VMware k místnímu prostředí](azure-vmware-solution-on-premises.md)
