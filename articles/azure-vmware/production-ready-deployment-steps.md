---
title: Plánování nasazení řešení Azure VMware
description: Tento článek popisuje pracovní postup nasazení řešení Azure VMware.  Konečný výsledek je prostředí připravené pro vytváření a migraci virtuálních počítačů.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462418"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Plánování nasazení řešení Azure VMware

Tento článek popisuje proces plánování, který identifikuje a shromažďuje informace, které budete používat během nasazování. Při plánování nasazení nezapomeňte zdokumentovat informace, které shromažďujete pro snadné reference během nasazování.

Kroky popsané v tomto rychlém startu poskytují prostředí připravené pro vytváření virtuálních počítačů (VM) a migrace v produkčním prostředí. 

>[!IMPORTANT]
>Před vytvořením prostředku řešení Azure VMware použijte článek [Povolení prostředku řešení Azure VMware](enable-azure-vmware-solution.md) k odeslání lístku podpory, který má přidělené hostitele. Jakmile tým podpory obdrží vaši žádost, trvá vám až pět pracovních dní, aby vaši žádost zkontroloval a rozdělila své hostitele. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více hostitelů, Projděte si stejný postup. 


## <a name="subscription"></a>Předplatné

Identifikujte předplatné, které plánujete použít k nasazení řešení Azure VMware.  Můžete buď vytvořit nové předplatné, nebo znovu použít stávající.

>[!NOTE]
>Předplatné musí být přidružené k plánu Azure pro Microsoft smlouva Enterprise nebo Cloud Solution Provider. Další informace najdete v tématu [Povolení prostředku řešení Azure VMware](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Skupina prostředků

Identifikujte skupinu prostředků, kterou chcete použít pro vaše řešení Azure VMware.  Obecně platí, že skupina prostředků je vytvořená speciálně pro řešení Azure VMware, ale můžete použít existující skupinu prostředků.

## <a name="region"></a>Oblast

Identifikujte oblast, kterou chcete nasadit řešení Azure VMware.  Další informace najdete v tématu [Příručka k produktům Azure, které jsou k dispozici v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Název prostředku

Zadejte název prostředku, který budete používat během nasazování.  Název prostředku je popisný a popisný název, ve kterém můžete název privátního cloudu řešení Azure VMware.

>[!IMPORTANT]
>Název nesmí být delší než 40 znaků. Pokud název překročí tento limit, nebudete moct vytvořit veřejné IP adresy pro použití s privátním cloudem. 

## <a name="size-hosts"></a>Velikost hostitelů

Identifikujte hostitele velikostí, které chcete použít při nasazení řešení Azure VMware.  Úplný seznam najdete v dokumentaci k [privátním cloudům a clusterům řešení Azure VMware](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-clusters-and-hosts"></a>Počet clusterů a hostitelů

V řešení Azure VMware nasadíte privátní cloud a vytvoříte víc clusterů. Pro vaše nasazení budete muset definovat počet clusterů a hostitele f, které chcete nasadit v každém clusteru. Minimální počet hostitelů na cluster je tři a maximum je 16. Maximální počet clusterů na jeden privátní cloud je čtyři. Maximální počet uzlů na jeden privátní cloud je 64.

Další informace najdete v dokumentaci k [privátnímu cloudu řešení Azure VMware a clusterům](concepts-private-clouds-clusters.md#clusters) .

>[!TIP]
>Cluster můžete kdykoli později roztáhnout, pokud potřebujete přejít nad rámec počátečního čísla nasazení.

## <a name="vcenter-admin-password"></a>heslo správce vCenter
Zadejte heslo správce vCenter. Během nasazení vytvoříte heslo správce vCenter. Heslo je přiřazeno k cloudadmin@vsphere.local účtu správce během sestavení vCenter. Pomocí těchto přihlašovacích údajů se přihlásíte k vCenter.

## <a name="nsx-t-admin-password"></a>Heslo správce NSX-T
Zadejte heslo správce NSX-T. Během nasazení vytvoříte heslo správce NSX-T. Heslo je přiřazeno k uživateli s oprávněními správce v účtu NSX během sestavení NSX. Pomocí těchto přihlašovacích údajů se přihlásíte ke Správci NSX-T.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segment IP adres pro správu privátního cloudu

Prvním krokem při plánování nasazení je naplánování segmentace IP. Řešení Azure VMware vyžaduje síť typu CIDR/22. Tento adresní prostor Carves do menších segmentů sítě (podsítí) a používá se pro funkce vCenter, VMware HCX, NSX-T a vMotion.

Tento blok síťových adres CIDR/22 se nesmí překrývat s jakýmkoli existujícím segmentem sítě, který už máte v místním prostředí nebo v Azure.

**Příklad:** 10.0.0.0/22

Řešení Azure VMware se připojuje k vašemu Microsoft Azure Virtual Network prostřednictvím interního okruhu Global Reach ExpressRoute (D-MSEE in a vizualizace). Tato funkce je součástí služby řešení Azure VMware a nebude se vám účtovat.

Další informace najdete v tématu [Kontrolní seznam pro plánování sítě](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment IP adres pro úlohy virtuálních počítačů

Identifikujte segment IP adres pro vytvoření první sítě pro úlohy (segment NSX) ve vašem privátním cloudu. Jinými slovy, budete muset vytvořit segment sítě v řešení Azure VMware, abyste mohli nasadit virtuální počítače v řešení Azure VMware.

I v případě, že plánujete rozšiřování sítí z místního prostředí do řešení Azure VMware (L2), je stále nutné vytvořit segment sítě, který ověřuje prostředí.

Nezapomeňte, že všechny vytvořené segmenty IP adres musí být jedinečné napříč vašimi Azure a místními nároky.
  
**Příklad:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifikace – segment IP adres pro úlohy virtuálních počítačů" border="false":::     

## <a name="optional-extend-networks"></a>Volitelné Rozšiřování sítí

Segmenty sítě můžete roztáhnout z místního prostředí do řešení Azure VMware a pokud to uděláte, identifikujte tyto sítě hned teď.  

Pamatujte na to, že:

- Pokud hodláte rozšiřovat sítě z místního prostředí, musí se tyto sítě připojit k [vSphere distribuovanému přepínači (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) v místním prostředí VMware.  
- Pokud sítě, které chcete rozšířit na [vSphere standardním přepínači](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), se nedají rozšířit.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Připojení Azure Virtual Network k řešení VMware Azure

V tomto kroku identifikujete bránu virtuální sítě ExpressRoute a podporu Virtual Network Azure, která se používá k připojení okruhu ExpressRoute Azure VMware Solution.  Okruh ExpressRoute usnadňuje připojení k privátnímu cloudu řešení Azure VMware a z něj do dalších služeb Azure, prostředků Azure a místních prostředí.

Můžete použít *existující* nebo *novou* bránu virtuální sítě ExpressRoute.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identita – Azure Virtual Network pro připojení řešení Azure VMware" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Použít existující bránu virtuální sítě ExpressRoute

Pokud použijete *existující* bránu virtuální sítě ExpressRoute, po nasazení privátního cloudu se vytvoří okruh ExpressRoute řešení Azure VMware. V takovém případě ponechte pole **Virtual Network** prázdné.  

Poznamenejte si, kterou bránu virtuální sítě ExpressRoute použijete a pokračujte k dalšímu kroku.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Vytvořit novou bránu virtuální sítě ExpressRoute

Když vytváříte *novou* bránu virtuální sítě ExpressRoute, můžete použít existující Virtual Network Azure nebo vytvořit novou.  

- Pro existující virtuální síť Azure:
   1. Ověřte, že ve virtuální síti nejsou žádné již existující brány virtuální sítě ExpressRoute. 
   1. Vyberte existující Virtual Network Azure ze seznamu **Virtual Network** .

- Novou Virtual Network Azure můžete vytvořit předem nebo během nasazování. V seznamu **Virtual Network** vyberte odkaz **vytvořit nový** .

Následující obrázek ukazuje obrazovku **vytvořit nasazení privátního cloudu** se zvýrazněným polem **Virtual Network** .

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Snímek obrazovky s možností nasazení řešení Azure VMware se zvýrazněným polem Virtual Network":::

>[!NOTE]
>Jakékoli virtuální sítě, která se bude používat nebo vytvořit, může vidět vaše místní prostředí a řešení Azure VMware. proto se ujistěte, že se nepřekrývají žádné segmenty IP používané v této virtuální síti a podsítích.

## <a name="vmware-hcx-network-segments"></a>Segmenty sítě VMware HCX

VMware HCX je technologie, která je součástí řešení Azure VMware. Primárními případy použití pro VMware HCX jsou migrace úloh a zotavení po havárii. Pokud plánujete jednu z těchto prací, je nejlepší naplánovat sítě nyní.   V opačném případě můžete přeskočit a pokračovat k dalšímu kroku.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste shromáždili a popsali potřebné informace, pokračujte k další části a vytvořte si privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení služby Azure VMware Solution](deploy-azure-vmware-solution.md)
