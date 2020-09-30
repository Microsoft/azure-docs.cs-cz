---
title: Plánování nasazení řešení Azure VMware
description: Tento článek popisuje pracovní postup nasazení řešení Azure VMware.  Konečný výsledek je prostředí připravené pro vytváření a migraci virtuálních počítačů.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 1a9ff313243650cc3f9c44be2eb1c62da5557955
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583269"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Plánování nasazení řešení Azure VMware

V tomto článku vám poskytneme proces plánování, který umožňuje identifikovat a shromažďovat data používaná během nasazení. [Pomocí kontrolního seznamu před nasazením](pre-deployment-checklist.md) zdokumentujte informace a pro snadné reference během nasazení.  

Procesy tohoto rychlého startu mají za následek vytváření virtuálních počítačů a migrace do prostředí připraveného pro produkční prostředí. 

>[!IMPORTANT]
>Před vytvořením prostředku řešení Azure VMware budete muset odeslat lístek podpory, abyste měli své uzly přidělené. Jakmile tým podpory obdrží vaši žádost, trvá vám až pět pracovních dní, aby vaši žádost zkontroloval a rozdělil vaše uzly. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více uzlů, Projděte si stejný postup. Další informace najdete v tématu [Povolení prostředku řešení Azure VMware](enable-azure-vmware-solution.md). 


## <a name="ip-address-segment"></a>Segment IP adres

Prvním krokem při plánování nasazení je naplánování segmentace IP.  Řešení Azure VMware ingestuje síť a/22, kterou zadáte. Pak je Carves do menších segmentů a pak tyto segmenty využije pro vCenter, VMware HCX, NSX-T a vMotion.

Řešení Azure VMware se připojuje k vašemu Microsoft Azure Virtual Network prostřednictvím interního okruhu ExpressRoute. Ve většině případů se k datovému centru připojí přes ExpressRoute Global Reach. 

Řešení Azure VMware, vaše stávající prostředí Azure a vaše místní prostředí, všechny trasy Exchange (obvykle). V takovém případě blok síťových adres CIDR/22, který v tomto kroku definujete, by neměl překrývat cokoli, co už máte v místním prostředí nebo Azure.

**Příklad:** 10.0.0.0/22

Další informace najdete v tématu [Kontrolní seznam pro plánování sítě](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segment IP adres pro úlohy virtuálních počítačů

Identifikujte segment IP adres pro vytvoření první sítě (segment NSX) ve vašem privátním cloudu.  Jinými slovy, chcete vytvořit segment sítě v řešení Azure VMware, abyste mohli nasadit virtuální počítače do řešení Azure VMware.   

I v případě, že plánujete rozšířit jenom sítě L2, vytvořte segment sítě, který bude užitečný pro ověřování prostředí.

Nezapomeňte, že všechny vytvořené segmenty IP adres musí být jedinečné napříč vašimi Azure a místními nároky.  

**Příklad:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::     

## <a name="optional-extend-networks"></a>Volitelné Rozšiřování sítí

Segmenty sítě můžete roztáhnout z místního prostředí do řešení Azure VMware a pokud to uděláte, identifikujte tyto sítě hned teď.  

Pamatujte na to, že:

- Pokud hodláte rozšiřovat sítě z místního prostředí, musí se tyto sítě připojit k [vSphere distribuovanému přepínači (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) v místním prostředí VMware.  
- Pokud sítě, které chcete rozšířit na [vSphere standardním přepínači](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), se nedají rozšířit.

## <a name="expressroute-global-reach-peering-network"></a>Síť partnerských vztahů ExpressRoute Global Reach

Identifikujte `/29` blok síťových adres CIDR, který je vyžadován pro partnerský vztah ExpressRoute Global REACH. Nezapomeňte, že všechny vytvořené segmenty IP adres musí být jedinečné napříč vaším řešením Azure VMware a místními nároky. IP adresy v tomto segmentu se používají na každém konci ExpressRoute Global Reach připojení pro připojení ExpressRoute okruhu řešení Azure VMware k místnímu okruhu ExpressRoute. 

**Příklad:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::

## <a name="subscription"></a>Předplatné

Identifikujte předplatné, které plánujete použít k nasazení řešení Azure VMware.  Můžete buď vytvořit nové předplatné, nebo znovu použít stávající.

>[!NOTE]
>Předplatné musí být přidružené k smlouva Enterprise Microsoft.

## <a name="resource-group"></a>Skupina prostředků

Identifikujte skupinu prostředků, kterou chcete použít pro vaše řešení Azure VMware.  Obecně platí, že skupina prostředků je vytvořená speciálně pro řešení Azure VMware, ale můžete použít existující skupinu prostředků.

## <a name="region"></a>Region (Oblast)

Identifikujte oblast, kterou chcete nasadit řešení Azure VMware.  Další informace najdete v tématu [Příručka k produktům Azure, které jsou k dispozici v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Název prostředku

Zadejte název prostředku, který budete používat během nasazování.  Název prostředku je popisný a popisný název, ve kterém můžete název privátního cloudu řešení Azure VMware.

## <a name="size-nodes"></a>Uzly velikosti

Identifikujte uzly velikosti, které chcete použít při nasazení řešení Azure VMware.  Úplný seznam najdete v dokumentaci k [privátním cloudům a clusterům řešení Azure VMware](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-hosts"></a>Počet hostitelů

Zadejte počet hostitelů, které chcete nasadit do privátního cloudu řešení Azure VMware.  Minimální počet uzlů je tři a maximum je 16 na cluster.  Další informace najdete v dokumentaci k [privátnímu cloudu řešení Azure VMware a clusterům](concepts-private-clouds-clusters.md#clusters) .

Cluster můžete kdykoli později roztáhnout, pokud potřebujete přejít nad rámec počátečního čísla nasazení.

## <a name="vcenter-admin-password"></a>heslo správce vCenter
Zadejte heslo správce vCenter.  Během nasazení vytvoříte heslo správce vCenter. Heslo je cloudadmin@vsphere.local účtu správce během sestavení vCenter. Použijete ho k přihlášení do vCenter.

## <a name="nsx-t-admin-password"></a>Heslo správce NSX-T
Zadejte heslo správce NSX-T.  Během nasazení vytvoříte heslo správce NSX-T. Heslo je přiřazeno k uživateli s oprávněními správce v účtu NSX během sestavení NSX. Použijete ho k přihlášení ke Správci NSX-T.

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure Virtual Network k připojení řešení Azure VMware

Aby bylo možné získat přístup k privátnímu cloudu řešení Azure VMware, musí se okruh ExpressRoute, který je součástí řešení Azure VMware, připojit k Azure Virtual Network.  Během nasazování můžete definovat novou virtuální síť nebo zvolit existující.

Okruh ExpressRoute z řešení Azure VMware se připojuje k bráně ExpressRoute v Azure Virtual Network, kterou definujete v tomto kroku.  

>[!IMPORTANT]
>Pokud zvolíte existující virtuální síť, musíte vybrat jednu, která nemá již existující podsíť brány.  

Pokud chcete okruh ExpressRoute připojit z řešení Azure VMware k existující bráně ExpressRoute, můžete to provést po nasazení.  

Proto je třeba v souhrnu propojit řešení Azure VMware s existující bránou Express Route?  

* **Ano** = Identifikujte virtuální síť, která se během nasazení nepoužívá.
* **No** = Identifikujte existující virtuální síť nebo vytvořte novou během nasazování.

Libovolný způsob, jak dokumentovat, co chcete udělat v tomto kroku.

>[!NOTE]
>Tato virtuální síť se zobrazuje v místním prostředí a řešení Azure VMware. proto se ujistěte, že se nepřekrývají žádné segmenty IP používané v této virtuální síti a podsítích.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identifikace – segment IP adres" border="false":::



## <a name="vmware-hcx-network-segments"></a>Segmenty sítě VMware HCX

VMware HCX je technologie, která je součástí řešení Azure VMware. Primárními případy použití pro VMware HCX jsou migrace úloh a zotavení po havárii. Pokud plánujete jednu z těchto prací, je nejlepší naplánovat sítě nyní.   V opačném případě můžete přeskočit a pokračovat k dalšímu kroku.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste shromáždili a popsali potřebné informace, pokračujte k další části a vytvořte si privátní cloud řešení Azure VMware.

> [!div class="nextstepaction"]
> [Nasazení řešení Azure VMware](deploy-azure-vmware-solution.md)