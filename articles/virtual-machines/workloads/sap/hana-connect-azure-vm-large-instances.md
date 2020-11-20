---
title: Nastavení připojení z virtuálních počítačů pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Nastavení připojení z virtuálních počítačů pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8f69f94c53227dc86fbbf9e9936a2fc3c97ec55
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967869"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Připojení virtuálních počítačů Azure k Velkým instancím HANA

Článek [co je SAP HANA v Azure (velké instance)?](./hana-overview-architecture.md) uvádí, že minimální nasazení velkých instancí HANA s aplikační vrstvou SAP v Azure vypadá takto:

![Virtuální síť Azure, která je připojená k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image1-architecture.png)

Při pohledu na stranu Azure Virtual Network je potřeba:

- Definice virtuální sítě Azure, do které budete nasazovat virtuální počítače vrstvy aplikace SAP.
- Definice výchozí podsítě ve virtuální síti Azure, která je ve skutečnosti ta, do které se virtuální počítače nasazují.
- Vytvořená virtuální síť Azure musí mít aspoň jednu podsíť virtuálních počítačů a jednu podsíť brány virtuální sítě ExpressRoute Azure. Těmto podsítím by se měly přiřadit rozsahy IP adres, které jsou uvedené a popsané v následujících oddílech.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Vytvoření virtuální sítě Azure pro velké instance HANA

>[!Note]
>Virtuální síť Azure pro velké instance HANA se musí vytvořit pomocí modelu nasazení Azure Resource Manager. Starší model nasazení Azure, který se běžně označuje jako model nasazení Classic, nepodporuje řešení pro velké instance HANA.

K vytvoření virtuální sítě můžete použít Azure Portal, PowerShell, šablonu Azure nebo rozhraní příkazového řádku Azure CLI. (Další informace najdete v tématu [vytvoření virtuální sítě pomocí Azure Portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). V následujícím příkladu se podíváme na virtuální síť, která je vytvořená pomocí Azure Portal.

Při odkazování na **adresní prostor** v této dokumentaci na adresní prostor, který může virtuální síť Azure používat. Tento adresní prostor je také rozsah adres, který virtuální síť používá pro šíření tras protokolu BGP. Tento **adresní prostor** se tady může zobrazit:

![Adresní prostor virtuální sítě Azure zobrazené v Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

V předchozím příkladu se v 10.16.0.0/16 dostala služba Azure Virtual Network místo toho velký a široké rozsah IP adres, který se má použít. Proto všechny rozsahy IP adres dalších podsítí v této virtuální síti můžou mít své rozsahy v rámci tohoto adresního prostoru. Obvykle to nedoporučujeme pro jednu virtuální síť v Azure. Ale pojďme se podívat na podsítě, které jsou definované ve službě Azure Virtual Network:

![Podsítě virtuální sítě Azure a jejich rozsahy IP adres](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Podíváme se na virtuální síť s první podsítí virtuálních počítačů (tady se říká "výchozí") a podsítí s názvem "GatewaySubnet".

Ve dvou předchozích grafikách **adresní prostor virtuální sítě** pokrývá jak **Rozsah IP adres podsítě virtuálního počítače Azure** , tak i brány virtuální sítě.

**Adresní prostor virtuální sítě** můžete omezit na konkrétní rozsahy používané každou podsítí. **Adresní prostor virtuální sítě** virtuální sítě můžete definovat také jako více konkrétních rozsahů, jak je znázorněno zde:

![Adresní prostor virtuální sítě Azure se dvěma mezerami](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

V takovém případě má **adresní prostor virtuální sítě** dva definované mezery. Jsou stejné jako rozsahy IP adres, které jsou definované pro rozsah IP adres podsítě virtuálního počítače Azure a brány virtuální sítě. 

Pro tyto podsítě klientů (podsítě virtuálních počítačů) můžete použít libovolný standardní pojmenování. Pro každou virtuální síť, která se připojuje k SAP HANA v Azure (velké instance) okruhu ExpressRoute, **musí mít vždycky jednu a jenom jednu podsíť brány** . **Tato podsíť brány musí mít název "GatewaySubnet"** , abyste se ujistili, že je správně umístěná brána ExpressRoute.

> [!WARNING] 
> Je důležité, aby podsíť brány měla vždy název "GatewaySubnet".

Můžete použít několik podsítí virtuálních počítačů a nesouvislých rozsahů adres. Rozsahy adres musí být zahrnuté v **adresním prostoru virtuální sítě** virtuální sítě. Můžou být v agregované podobě. Můžou být taky v seznamu přesných rozsahů podsítí virtuálních počítačů a podsítě brány.

Toto je souhrn důležitých faktů o službě Azure Virtual Network, která se připojuje k velkým instancím HANA:

- V případě, že provádíte počáteční nasazení velkých instancí HANA, musíte do Microsoftu odeslat **adresní prostor virtuální sítě** . 
- **Adresní prostor virtuální sítě** může být jeden větší rozsah, který pokrývá rozsahy pro rozsah IP adres podsítě pro virtuální počítač Azure a bránu virtuální sítě.
- Nebo můžete odeslat více rozsahů, které pokrývají různé rozsahy IP adres pro rozsahy IP adres podsítí virtuálních počítačů a rozsah IP adres brány virtuální sítě.
- Definovaný **adresní prostor virtuální sítě** se používá pro šíření směrování BGP.
- Název podsítě brány musí být: **"GatewaySubnet"**.
- Adresní prostor se používá jako filtr na straně velké instance HANA a povoluje nebo zakazuje provoz pro jednotky velkých instancí HANA z Azure. Informace o směrování protokolu BGP virtuální sítě Azure a rozsahy IP adres, které jsou nakonfigurované pro filtrování na straně velkých instancí HANA, se musí shodovat. V opačném případě mohou nastat problémy s připojením.
- Existuje několik podrobností o podsíti brány, které jsou popsány později v části **připojení virtuální sítě k ExpressRoute rozsáhlých instancí.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Budou definovány různé rozsahy IP adres. 

Některé rozsahy IP adres, které jsou nezbytné pro nasazení velkých instancí HANA, již byly zavedeny. Existují však i další rozsahy IP adres, které jsou také důležité. Do společnosti Microsoft není třeba odeslat všechny následující rozsahy IP adres. Před odesláním žádosti o počáteční nasazení je ale nutné je definovat:

- **Adresní prostor virtuální sítě**: **adresní prostor virtuální sítě** je rozsahy IP adres, které přiřadíte do parametru adresní prostor ve virtuálních sítích Azure. Tyto sítě se připojují k prostředí SAP HANA velké instance. Doporučujeme, aby tento parametr adresního prostoru byl víceřádková hodnota. Měl by se skládat z rozsahu podsítě virtuálního počítače Azure a rozsahů podsítí brány Azure. Tento rozsah podsítě byl zobrazen v předchozí grafice. Nesmí se překrývat s vaším místním nebo serverovým fondem IP adres nebo s rozsahy adres ER-P2P. Jak získám tyto rozsahy IP adres? Váš podnikový síťový tým nebo poskytovatel služeb by měl poskytovat jeden nebo víc rozsahů IP adres, které se ve vaší síti nepoužívají. Například podsíť virtuálního počítače Azure je 10.0.1.0/24 a podsíť vaší podsítě Azure Gateway je 10.0.2.0/28.  Doporučujeme, aby adresní prostor služby Azure Virtual Network byl definován jako: 10.0.1.0/24 a 10.0.2.0/28. I když je možné hodnoty adresního prostoru agregovat, doporučujeme, abyste je shodovali s rozsahy podsítí. Tímto způsobem se můžete vyhnout náhodnému používání nepoužívaných rozsahů IP adres v rámci větších adresních prostorů jinde ve vaší síti. **Adresní prostor virtuální sítě je rozsah IP adres. Při žádosti o počáteční nasazení je třeba odeslat společnosti Microsoft**.
- **Rozsah IP adres podsítě virtuálních počítačů Azure:** Tento rozsah IP adres je ten, který přiřadíte k parametru podsítě virtuální sítě Azure. Tento parametr je ve vaší virtuální síti Azure a připojuje se k prostředí SAP HANA velké instance. Tento rozsah IP adres se používá k přiřazování IP adres k virtuálním počítačům Azure. IP adresy z tohoto rozsahu se můžou připojovat k vašim SAP HANAm na serverech s velkým počtem instancí. V případě potřeby můžete použít několik podsítí virtuálních počítačů Azure. Pro každou podsíť virtuálních počítačů Azure doporučujeme blok CIDR/24. Tento rozsah adres musí být součástí hodnot, které se používají v adresním prostoru virtuální sítě Azure. Jak získám tento rozsah IP adres? Váš podnikový síťový tým nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se ve vaší síti nepoužívá.
- **Rozsah IP adres podsítě brány virtuální sítě:** V závislosti na funkcích, které plánujete použít, je doporučená velikost:
   - Ultra-Performance ExpressRoute Gateway:/26 blok adres – vyžaduje se pro třídu Type II SKU.
   - Koexistence sítě VPN a ExpressRoute pomocí vysoce výkonné brány virtuální sítě ExpressRoute (nebo menší):/27 blok adres.
   - Všechny ostatní situace:/28 blok adresy. Tento rozsah adres musí být součástí hodnot, které se používají v hodnotách adresního prostoru virtuální sítě. Tento rozsah adres musí být součástí hodnot, které se používají v hodnotách adresního prostoru virtuální sítě Azure, které odesíláte do Microsoftu. Jak získám tento rozsah IP adres? Váš podnikový síťový tým nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá. 
- **Rozsah adres pro připojení ER-P2P:** Tento rozsah je rozsahem IP adres pro vaše SAP HANA připojení P2P pro velké instance ExpressRoute (ER). Tento rozsah IP adres musí být rozsah IP adres CIDR/29. Tento rozsah se nesmí překrývat s místními nebo jinými rozsahy IP adres Azure. Tento rozsah IP adres se používá k nastavení připojení ER z virtuální brány ExpressRoute na servery SAP HANA velkých instancí. Jak získám tento rozsah IP adres? Váš podnikový síťový tým nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá. **Tento rozsah je rozsah IP adres. Při žádosti o počáteční nasazení je třeba odeslat společnosti Microsoft**.  
- **Rozsah adres fondu IP adres serveru:** Tento rozsah IP adres se používá k přiřazení jednotlivých IP adres serverům rozsáhlých instancí HANA. Doporučená velikost podsítě je blok CIDR/24. V případě potřeby může být menší než 64 IP adres. Od tohoto rozsahu jsou prvních 30 IP adres rezervované pro použití Microsoftem. Pokud zvolíte velikost rozsahu, ujistěte se, že jste tento fakt vyhlédli. Tento rozsah se nesmí překrývat s místními nebo jinými IP adresami Azure. Jak získám tento rozsah IP adres? Váš podnikový síťový tým nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá.  **Tento rozsah je rozsah IP adres, který je potřeba odeslat společnosti Microsoft při žádosti o počáteční nasazení**.

Volitelné rozsahy IP adres, které se nakonec musí odeslat společnosti Microsoft:

- Pokud se rozhodnete použít [Global REACH ExpressRoute](../../../expressroute/expressroute-global-reach.md) , abyste mohli povolit přímé směrování z místních na velké jednotky instancí pro Hana, musíte rezervovat jiný rozsah IP adres/29. Tento rozsah se nesmí překrývat s žádným z dalších rozsahů IP adres, které jste definovali dříve.
- Pokud se rozhodnete použít [Global REACH ExpressRoute](../../../expressroute/expressroute-global-reach.md) , abyste mohli povolit přímé směrování z klienta služby velká instance Hana v jedné oblasti Azure do jiného tenanta rozsáhlých instancí Hana v jiné oblasti Azure, je potřeba rezervovat jiný rozsah IP adres/29. Tento rozsah se nesmí překrývat s žádným z dalších rozsahů IP adres, které jste definovali dříve.

Další informace o ExpressRoute Global Reach a využití o velkých instancích HANA najdete v dokumentech:

- [Síťová architektura SAP HANA (velké instance)](./hana-network-architecture.md)
- [Připojení virtuální sítě k velkým instancím HANA](./hana-connect-vnet-express-route.md)
 
Je nutné definovat a naplánovat rozsahy IP adres, které byly popsány dříve. Nemusíte je ale přenášet do Microsoftu. Rozsahy IP adres, které se musí pojmenovat Microsoftu:

- Adresní prostory virtuální sítě Azure
- Rozsah adres pro připojení ER-P2P
- Rozsah adres fondu IP adres serveru

Pokud přidáte další virtuální sítě, které se potřebují připojit k velkým instancím HANA, musíte odeslat nový adresní prostor služby Azure Virtual Network, který přidáváte do Microsoftu. 

Následuje příklad různých rozsahů a několik ukázkových rozsahů, které je třeba nakonfigurovat a nakonec poskytnout společnosti Microsoft. Hodnota adresního prostoru virtuální sítě Azure není v prvním příkladu agregovaná. Je ale definovaný z rozsahu prvního rozsahu IP adres podsítě virtuálních počítačů Azure a rozsahu IP adres podsítě brány virtuální sítě. 

Pokud nakonfigurujete a odešlete další rozsahy IP adres dalších podsítí virtuálních počítačů jako součást adresního prostoru virtuální sítě Azure, můžete v rámci služby Azure Virtual Network použít několik podsítí virtuálních počítačů.

![Rozsahy IP adres vyžadované v SAP HANA v Azure (velké instance) minimální nasazení](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Obrázek nezobrazuje další rozsahy IP adres, které jsou požadovány pro volitelné použití Global Reach ExpressRoute.

Data, která odesíláte do Microsoftu, můžete také agregovat. V takovém případě zahrnuje adresní prostor virtuální sítě Azure jenom jednu mezeru. Pomocí rozsahů IP adres v předchozím příkladu může agregovaný adresní prostor virtuální sítě vypadat jako na následujícím obrázku:

![Druhá možnost rozsahů IP adres, které vyžaduje SAP HANA v Azure (velké instance) minimální nasazení](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Místo dvou menších rozsahů, které definují adresní prostor služby Azure Virtual Network, máme v tomto příkladu jeden větší rozsah, který pokrývá 4096 IP adres. Taková velká definice adresního prostoru opouští místo nevyužitých rozsahů. Vzhledem k tomu, že se hodnoty adresního prostoru virtuální sítě používají pro šíření tras protokolu BGP, využití nevyužitých rozsahů v místním prostředí nebo jinde ve vaší síti může způsobit problémy s směrováním. Obrázek nezobrazuje další rozsahy IP adres, které jsou požadovány pro volitelné použití Global Reach ExpressRoute.

Doporučujeme, abyste si adresní prostor přesně zarovnali se skutečným adresním prostorem podsítě, který používáte. V případě potřeby můžete později přidat nové hodnoty adresního prostoru, aniž by došlo k výpadku virtuální sítě.
 
> [!IMPORTANT] 
> Každý rozsah IP adres v ER-P2P, fondu IP serverů a adresní prostor virtuální sítě Azure **se nesmí překrývat s** jiným rozsahem, který se používá ve vaší síti. Každý musí být diskrétní. Jak je znázorněno v předchozích grafických grafech, nemohou být zároveň podsítí žádného jiného rozsahu. Pokud dojde k překrytí mezi rozsahy, virtuální síť Azure se nemusí připojit k okruhu ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Další kroky po definování rozsahů adres
Po definování rozsahů IP adres musí dojít k následujícím akcím:

1. Odešlete rozsahy IP adres pro adresní prostor služby Azure Virtual Network, připojení ER-P2P a rozsah adres fondu IP adres fondu, společně s ostatními daty, která byla uvedena na začátku dokumentu. V tomto okamžiku můžete také začít vytvářet virtuální síť a podsítě virtuálních počítačů. 
2. Okruh ExpressRoute se vytvoří od Microsoftu mezi vaším předplatným Azure a razítkem velké instance HANA.
3. V rámci razítka velké instance od Microsoftu se vytvoří síť tenanta.
4. Microsoft nakonfiguruje sítě v infrastruktuře SAP HANA v Azure (velké instance), aby přijímaly IP adresy z adresního prostoru virtuální sítě Azure, který komunikuje s velkými instancemi HANA.
5. V závislosti na konkrétní SAP HANA v SKU Azure (velké instance), které jste si koupili, Microsoft přiřadí výpočetní jednotku v klientské síti. Také přiděluje a napojí úložiště a nainstaluje operační systém (SUSE nebo Red Hat Linux). IP adresy pro tyto jednotky se vyberou mimo rozsah adres fondu IP adres, který jste odeslali společnosti Microsoft.

Na konci procesu nasazení zajišťuje společnost Microsoft následující data:
- Informace potřebné k připojení virtuálních sítí Azure k okruhu ExpressRoute, který propojuje virtuální sítě Azure s velkými instancemi HANA:
     - Autorizační klíč (y)
     - ExpressRoute PeerID
- Data pro přístup k velkým instancím HANA po vytvoření okruhu ExpressRoute a virtuální sítě Azure.

V dokumentu [SAP HANA v instalaci Azure (velké instance)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)můžete také najít posloupnost připojení velkých instancí Hana. V příkladu nasazení v tomto dokumentu se zobrazuje řada z následujících kroků. 

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu připojení virtuální sítě k ExpressRoute rozsáhlých instancí v Hana](hana-connect-vnet-express-route.md).
