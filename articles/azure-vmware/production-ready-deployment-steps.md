---
title: Plánování nasazení řešení Azure VMware
description: Tento článek popisuje pracovní postup nasazení řešení Azure VMware.  Konečný výsledek je prostředí připravené pro vytváření a migraci virtuálních počítačů.
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: f9d49d7ff8109364c9fc1eee4388b30ccc1a61b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733652"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Plánování nasazení řešení Azure VMware

Tento článek vám poskytne proces plánování, pomocí kterého můžete identifikovat a shromažďovat data používaná během nasazení. Při plánování nasazení nezapomeňte zdokumentovat informace, které shromažďujete pro snadné reference během nasazování.

Procesy tohoto rychlého startu mají za následek vytváření virtuálních počítačů a migrace v prostředí připraveném pro produkční prostředí. 

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

## <a name="number-of-hosts"></a>Počet hostitelů

Zadejte počet hostitelů, které chcete nasadit do privátního cloudu řešení Azure VMware.  Minimální počet hostitelů je tři a maximum je 16 na cluster.  Další informace najdete v dokumentaci k [privátnímu cloudu řešení Azure VMware a clusterům](concepts-private-clouds-clusters.md#clusters) .

Cluster můžete kdykoli později roztáhnout, pokud potřebujete přejít nad rámec počátečního čísla nasazení.

## <a name="ip-address-segment"></a>Segment IP adres

Prvním krokem při plánování nasazení je naplánování segmentace IP.  Řešení Azure VMware ingestuje síť a/22, kterou zadáte. Pak je Carves do menších segmentů a pak tyto segmenty využije pro vCenter, VMware HCX, NSX-T a vMotion.

Řešení Azure VMware se připojuje k vašemu Microsoft Azure Virtual Network prostřednictvím interního okruhu ExpressRoute. Ve většině případů se k datovému centru připojí ExpressRoute Global Reach. 

Řešení Azure VMware, vaše stávající prostředí Azure a vaše místní prostředí, všechny trasy Exchange (obvykle). V takovém případě blok síťových adres CIDR/22, který v tomto kroku definujete, by neměl překrývat cokoli, co už máte v místním prostředí nebo Azure.

**Příklad:** 10.0.0.0/22

Další informace najdete v tématu [Kontrolní seznam pro plánování sítě](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment IP adres pro úlohy virtuálních počítačů

Identifikujte segment IP adres pro vytvoření první sítě (segment NSX) ve vašem privátním cloudu.  Jinými slovy, chcete vytvořit segment sítě v řešení Azure VMware, abyste mohli nasadit virtuální počítače do řešení Azure VMware.   

I v případě, že plánujete rozšířit jenom sítě L2, vytvořte segment sítě, který bude toto prostředí ověřovat.

Nezapomeňte, že všechny vytvořené segmenty IP adres musí být jedinečné napříč vašimi Azure a místními nároky.  

**Příklad:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifikace – segment IP adres pro úlohy virtuálních počítačů" border="false":::     

## <a name="optional-extend-networks"></a>Volitelné Rozšiřování sítí

Segmenty sítě můžete roztáhnout z místního prostředí do řešení Azure VMware a pokud to uděláte, identifikujte tyto sítě hned teď.  

Pamatujte na to, že:

- Pokud hodláte rozšiřovat sítě z místního prostředí, musí se tyto sítě připojit k [vSphere distribuovanému přepínači (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) v místním prostředí VMware.  
- Pokud sítě, které chcete rozšířit na [vSphere standardním přepínači](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), se nedají rozšířit.

## <a name="attach-virtual-network-to-azure-vmware-solution"></a>Připojení virtuální sítě k řešení VMware Azure

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
