---
title: Nastavení připojení z virtuálních počítačů do SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Nastavení připojení z virtuálních počítačů pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74224730"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Připojení virtuálních počítačů Azure k Velkým instancím HANA

Článek [Co je SAP HANA v Azure (velké instance)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) uvádí, že minimální nasazení velkých instancí HANA s aplikační vrstvou SAP v Azure vypadá takto:

![Virtuální síť Azure připojená k SAP HANA v Azure (velké instance) a místně](./media/hana-overview-architecture/image1-architecture.png)

Podíváme-li se blíže na straně virtuální sítě Azure, je potřeba:

- Definice virtuální sítě Azure, do které budete nasazovat virtuální počítače aplikační vrstvy SAP.
- Definice výchozí podsítě ve virtuální síti Azure, která je ve skutečnosti ten, do kterého se nasazují virtuální počítače.
- Vytvořená virtuální síť Azure musí mít alespoň jednu podsíť virtuálního počítače a jednu podsíť virtuální sítě Azure ExpressRoute. Těmto podsítím by měly být přiřazeny rozsahy adres IP, jak jsou specifikovány a popsány v následujících částech.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Vytvoření virtuální sítě Azure pro velké instance HANA

>[!Note]
>Virtuální síť Azure pro velké instance HANA musí být vytvořená pomocí modelu nasazení Azure Resource Manageru. Starší model nasazení Azure, běžně známý jako klasický model nasazení, není podporován řešením velké instance HANA.

K vytvoření virtuální sítě můžete použít portál Azure, PowerShell, šablonu Azure nebo azure cli. (Další informace najdete [v tématu Vytvoření virtuální sítě pomocí portálu Azure).](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) V následujícím příkladu se podíváme na virtuální síť, která se vytvoří pomocí portálu Azure.

Když se odkazuje na **adresní prostor** v této dokumentaci, do adresního prostoru, který virtuální síť Azure je povoleno používat. Tento adresní prostor je také rozsah adres, který virtuální síť používá pro šíření trasy Protokolu BGP. Tento **adresní prostor** si můžete prohlédnout zde:

![Adresní prostor virtuální sítě Azure zobrazené na webu Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

V předchozím příkladu s 10.16.0.0/16 virtuální sítě Azure dostal poměrně velký a široký rozsah IP adres k použití. Proto všechny rozsahy IP adres následujících podsítí v rámci této virtuální sítě mohou mít své rozsahy v rámci tohoto adresního prostoru. Obvykle nedoporučujeme tak velký rozsah adres pro jednu virtuální síť v Azure. Ale podívejme se na podsítě, které jsou definovány ve virtuální síti Azure:

![Podsítě virtuálních sítí Azure a jejich rozsahy IP adres](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Podíváme se na virtuální síť s první podsítí virtuálních počítače (zde nazývanou "výchozí") a podsítí s názvem "GatewaySubnet".

Ve dvou předchozích grafikách **pokrývá adresní prostor virtuální sítě** rozsah IP adres **podsítě virtuálního počítače Azure** i rozsah virtuálnísítě virtuální sítě.

**Adresní prostor virtuální sítě** můžete omezit na konkrétní rozsahy používané jednotlivými podsítěmi. Adresní prostor **virtuální sítě** virtuální sítě virtuální sítě můžete také definovat jako více specifických rozsahů, jak je znázorněno zde:

![Adresní prostor virtuální sítě Azure se dvěma prostory](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

V tomto případě **má adresní prostor virtuální sítě** definovány dva prostory. Jsou stejné jako rozsahy IP adres, které jsou definovány pro rozsah IP adres podsítě virtuálního počítače Azure a brány virtuální sítě. 

Pro tyto podsítě tenanta (podsítě virtuálních zařízení) můžete použít libovolný standard pojmenování, který se vám líbí. Však **musí být vždy jeden a pouze jeden podsíť brány pro každou virtuální síť,** která se připojuje k SAP HANA v Azure (velké instance) ExpressRoute okruhu. **Tato podsíť brány musí mít název "GatewaySubnet",** aby bylo zajištěno, že brána ExpressRoute je správně umístěna.

> [!WARNING] 
> Je důležité, aby podsíť brány byla vždy pojmenována "GatewaySubnet".

Můžete použít více podsítí virtuálních vod a nesouvislé rozsahy adres. Tyto rozsahy adres musí být pokryty **adresním prostorem virtuální sítě** virtuální sítě. Mohou být v souhrnné podobě. Mohou být také v seznamu přesných rozsahů podsítí virtuálních vod a podsítě brány.

Následuje přehled důležitých faktů o virtuální síti Azure, která se připojuje k rozsáhlým instancím HANA:

- Při počátečním nasazení velkých instancí HANA je nutné společnosti Microsoft odeslat **adresní prostor virtuální sítě.** 
- **Adresní prostor virtuální sítě** může být jeden větší rozsah, který pokrývá rozsahy pro rozsah IP adres podsítě virtuálního počítače Azure a brány virtuální sítě.
- Nebo můžete odeslat více rozsahů, které pokrývají různé rozsahy IP adres v rozsahu IP adres virtuálních sítí a rozsah ip adres brány virtuální sítě.
- Definovaný **adresní prostor virtuální sítě** se používá pro šíření směrování protokolu BGP.
- Název podsítě brány musí být: **"GatewaySubnet"**.
- Adresní prostor se používá jako filtr na straně velké instance HANA k povolení nebo zakázání provozu na jednotky velké instance HANA z Azure. Informace o směrování protokolu BGP virtuální sítě Azure a rozsahy IP adres, které jsou nakonfigurované pro filtrování na straně velké instance HANA, by se měly shodovat. V opačném případě může dojít k problémům s připojením.
- Existují některé podrobnosti o podsíti brány, které jsou popsány později, v části **Připojení virtuální sítě k HANA Velké instance ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Různé rozsahy IP adres, které mají být definovány 

Některé rozsahy IP adres, které jsou nezbytné pro nasazení velkých instancí HANA, byly již zavedeny. Ale existuje více rozsahů IP adres, které jsou také důležité. Ne všechny následující rozsahy IP adres je třeba odeslat společnosti Microsoft. Před odesláním požadavku na počáteční nasazení je však nutné je definovat:

- **Adresní prostor virtuální sítě**: **Adresní prostor virtuální sítě** je rozsahy IP adres, které přiřadíte parametru adresního prostoru ve virtuálních sítích Azure. Tyto sítě se připojují k prostředí velké instance SAP HANA. Doporučujeme, aby tento parametr adresního prostoru byla víceřádková hodnota. Měl by se skládat z rozsahu podsítě virtuálního počítače Azure a rozsahů podsítí brány Azure. Tento rozsah podsítí byl zobrazen v předchozí grafice. Nesmí se překrývat s místním nebo serverovým fondem IP nebo rozsahy adres ER-P2P. Jak se vám tyto rozsahy IP adres?? Tým podnikové sítě nebo poskytovatel služeb by měl poskytnout jeden nebo více rozsahů IP adres, které se v síti nepoužívají. Například podsíť virtuálního počítače Azure je 10.0.1.0/24 a podsíť podsítě brány Azure je 10.0.2.0/28.  Doporučujeme, aby váš adresní prostor virtuální sítě Azure byl definován jako: 10.0.1.0/24 a 10.0.2.0/28. Přestože lze agregovat hodnoty adresního prostoru, doporučujeme je přiřazovat k rozsahům podsítí. Tímto způsobem se můžete omylem vyhnout opakovanému použití nepoužívaných rozsahů IP adres ve větších adresních prostorech jinde v síti. **Adresní prostor virtuální sítě je rozsah adres IP. Je třeba předložit společnosti Microsoft, když požádáte o počáteční nasazení**.
- **Rozsah ip adres podsítě virtuálního počítače Azure:** Tento rozsah IP adres je ten, který přiřadíte k parametru podsítě virtuální sítě Azure. Tento parametr je ve vaší virtuální síti Azure a připojuje se k prostředí SAP HANA Large Instance. Tento rozsah IP adres se používá k přiřazení IP adres vašim virtuálním počítačům Azure. Ip adresy mimo tento rozsah se mohou připojit k serveru velké instance SAP HANA. V případě potřeby můžete použít více podsítí virtuálních virtuálních vrátk Azure. Pro každou podsíť virtuálního počítače Azure doporučujeme blok CIDR /24. Tento rozsah adres musí být součástí hodnot, které se používají v adresním prostoru virtuální sítě Azure. Jak získat tento rozsah IP adres? Tým podnikové sítě nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se ve vaší síti nepoužívá.
- **Rozsah ip adres podsítě virtuální sítě:** V závislosti na funkcích, které chcete použít, je doporučená velikost:
   - Ultravýkonná brána ExpressRoute: /26 adresní blok -- vyžadovánpro třídu SKU typu II.
   - Koexistence s VPN a ExpressRoute pomocí vysoce výkonné brány virtuální sítě ExpressRoute (nebo menší): /27 blok adres.
   - Všechny ostatní situace: /28 adresní blok. Tento rozsah adres musí být součástí hodnot použitých v hodnotách "adresní prostor virtuální sítě". Tento rozsah adres musí být součástí hodnot, které se používají v hodnotách adresního prostoru virtuální sítě Azure, které odešlete společnosti Microsoft. Jak získat tento rozsah IP adres? Tým podnikové sítě nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá. 
- **Rozsah adres pro připojení ER-P2P:** Tento rozsah je rozsah IP pro připojení SAP HANA Large Instance ExpressRoute (ER) P2P. Tento rozsah ADRES IP musí být rozsah IP adres /29 CIDR. Tento rozsah se nesmí překrývat s místními nebo jinými rozsahy IP adres Azure. Tento rozsah IP adres se používá k nastavení připojení er z virtuální brány ExpressRoute na servery sap hana velké instance. Jak získat tento rozsah IP adres? Tým podnikové sítě nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá. **Tento rozsah je rozsah IP adres. Je třeba předložit společnosti Microsoft, když požádáte o počáteční nasazení**.  
- **Rozsah adres fondu IP serveru:** Tento rozsah IP adres se používá k přiřazení jednotlivé adresy IP serverům velkých instancí HANA. Doporučená velikost podsítě je blok CIDR /24. V případě potřeby může být menší, s až 64 IP adresami. Z tohoto rozsahu je prvních 30 adres IP vyhrazeno pro použití společností Microsoft. Ujistěte se, že jste účet pro tuto skutečnost při výběru velikosti rozsahu. Tento rozsah se nesmí překrývat s místními nebo jinými IP adresami Azure. Jak získat tento rozsah IP adres? Tým podnikové sítě nebo poskytovatel služeb by měl poskytnout rozsah IP adres, který se v síti aktuálně nepoužívá.  **Tento rozsah je rozsah IP adres, který je třeba odeslat společnosti Microsoft při žádosti o počáteční nasazení**.

Volitelné rozsahy IP adres, které nakonec je třeba odeslat společnosti Microsoft:

- Pokud se rozhodnete použít [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) k povolení přímého směrování z místních jednotek velké instance hana, musíte rezervovat další rozsah IP adres /29. Tento rozsah se nesmí překrývat s žádným z ostatních rozsahů IP adres, které jste definovali dříve.
- Pokud se rozhodnete použít [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) k povolení přímého směrování z tenanta velké instance HANA v jedné oblasti Azure do jiného tenanta velké instance HANA v jiné oblasti Azure, musíte rezervovat další rozsah IP adres /29. Tento rozsah se nesmí překrývat s žádným z ostatních rozsahů IP adres, které jste definovali dříve.

Další informace o globálním dosahu ExpressRoute a využití velkých instancí HANA najdete v dokumentech:

- [Architektura sítě SAP HANA (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Připojení virtuální sítě k velkým instancím HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Je třeba definovat a naplánovat rozsahy IP adres, které byly popsány dříve. Není však nutné je všechny přenášet společnosti Microsoft. Rozsahy IP adres, které jsou nutné k pojmenování společnosti Microsoft, jsou:

- Adresní prostory virtuální sítě Azure
- Rozsah adres pro připojení ER-P2P
- Rozsah adres fondu IP serverů

Pokud přidáte další virtuální sítě, které se potřebují připojit k velké instance HANA, budete muset odeslat nový adresní prostor virtuální sítě Azure, který přidáváte do Microsoftu. 

Následuje příklad různých rozsahů a některé příklady rozsahy, jak je třeba nakonfigurovat a případně poskytnout společnosti Microsoft. Hodnota adresního prostoru virtuální sítě Azure se v prvním příkladu neagreguje. Je však definována z rozsahů prvního rozsahu IP adres podsítě virtuálního počítače Azure a rozsahu ip adres podsítě virtuální sítě. 

Více podsítí virtuálních počítačů v rámci virtuální sítě Azure můžete použít při konfiguraci a odeslání dalších rozsahů IP adres dalších podsítí virtuálních počítačů jako součást adresního prostoru virtuální sítě Azure.

![Rozsahy IP adres požadované v SAP HANA v Azure (velké instance) minimální nasazení](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Grafika nezobrazuje další rozsahy IP adres, které jsou požadovány pro volitelné použití expressroute global reach.

Můžete také agregovat data, která odešlete společnosti Microsoft. V takovém případě adresní prostor virtuální sítě Azure obsahuje jenom jedno místo. Pomocí ip adresy se pohybuje od předchozího příkladu agregované virtuální sítě adresní prostor může vypadat jako následující obrázek:

![Druhá možnost rozsahů IP adres požadovaných v SAP HANA v Azure (velké instance) minimální nasazení](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

V příkladu namísto dvou menších rozsahů, které definovaly adresní prostor virtuální sítě Azure, máme jeden větší rozsah, který pokrývá 4096 IP adres. Taková velká definice adresního prostoru ponechává některé poměrně velké rozsahy nevyužité. Vzhledem k tomu, že hodnota adresního prostoru virtuální sítě se používá pro šíření trasy Protokolu BGP, může použití nepoužívaných rozsahů v místním prostředí nebo jinde v síti způsobit problémy se směrováním. Grafika nezobrazuje další rozsahy IP adres, které jsou požadovány pro volitelné použití expressroute global reach.

Doporučujeme zachovat adresní prostor pevně zarovnaný se skutečným adresním prostorem podsítě, který používáte. V případě potřeby, aniž by došlo k prostojů ve virtuální síti, můžete vždy přidat nové hodnoty adresního prostoru později.
 
> [!IMPORTANT] 
> Každý rozsah IP adres v ER-P2P, fond IP serveru a adresní prostor virtuální sítě Azure **se nesmí** překrývat mezi sebou nebo s jiným rozsahem používaným ve vaší síti. Každý musí být diskrétní. Jak ukazují dvě předchozí grafické show, nemohou být také podsítí žádného jiného rozsahu. Pokud dojde k překrývání mezi rozsahy, virtuální síť Azure se nemusí připojit k okruhu ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Další kroky po definování rozsahů adres
Po definování rozsahů IP adres je třeba se stát následujícími věcmi:

1. Odešlete rozsahy IP adres pro adresní prostor virtuální sítě Azure, připojení ER-P2P a rozsah adres fondu IP serveru spolu s dalšími daty, která byla uvedena na začátku dokumentu. V tomto okamžiku můžete také začít vytvářet virtuální síť a podsítě virtuálních zařízení. 
2. Okruh ExpressRoute je vytvořen společností Microsoft mezi předplatným Azure a razítkem velké instance HANA.
3. Síť klienta je vytvořena na razítku velké instance společností Microsoft.
4. Microsoft konfiguruje sítě v infrastruktuře SAP HANA v Azure (velké instance) pro přijímání IP adres z adresního prostoru virtuální sítě Azure, který komunikuje s velkými instancemi HANA.
5. V závislosti na konkrétní SAP HANA na Azure (velké instance) SKU, které jste si koupili, Microsoft přiřadí výpočetní jednotky v síti klienta. Také přiděluje a připojuje úložiště a nainstaluje operační systém (SUSE nebo Red Hat Linux). Adresy IP pro tyto jednotky jsou převzaty z rozsahu adres fondu IP serveru, který jste odeslali společnosti Microsoft.

Na konci procesu nasazení vám společnost Microsoft doručí následující data:
- Informace potřebné k připojení virtuálních sítí Azure k okruhu ExpressRoute, který propojuje virtuální sítě Azure s velkými instancemi HANA:
     - Autorizační klíč (y)
     - Evi-iD trasy ExpressRoute
- Data pro přístup k velkým instancím HANA po vytvoření okruhu ExpressRoute a virtuální sítě Azure.

Pořadí připojení velkých instancí HANA najdete také v dokumentu [SAP HANA v Azure (velké instance) Setup](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Mnoho z následujících kroků jsou uvedeny v příkladu nasazení v tomto dokumentu. 

## <a name="next-steps"></a>Další kroky

- Viz [Připojení virtuální sítě k hana velké instance ExpressRoute](hana-connect-vnet-express-route.md).
