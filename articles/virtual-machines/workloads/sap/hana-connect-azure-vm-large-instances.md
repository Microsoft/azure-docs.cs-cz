---
title: Nastavení připojení z virtuálních počítačů do systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Nastavení připojení z virtuálních počítačů pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90d920a501d27ca21b1c2b7b7f5491cebb2c2822
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233714"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Připojení virtuálních počítačů Azure pro velké instance HANA

Jak již bylo uvedeno v [architektura v Azure a SAP HANA (velké instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), minimálního nasazení velkých instancích HANA s aplikační vrstvě SAP v Azure vypadá takto:

![Virtuální síť Azure připojení k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Hledání blíže na straně virtuální síť Azure, Uvědomujeme potřeba:

- Definice služby Azure virtual network, do které se chystáte nasadit virtuální počítače ve vrstvě aplikace SAP.
- Definice výchozí podsíť ve virtuální síti Azure, který je ve skutečnosti jedna, do které jsou nasazené virtuální počítače.
- Virtuální síť Azure, který je vytvořen musí mít alespoň jednu podsíť virtuálních počítačů a jednu podsíť brány virtuální sítě Azure ExpressRoute. Tyto podsítě měla být přiřazena rozsahy IP adres jako zadaný a popsané v následujících částech.

Pojďme se podívat trochu podrobněji při vytváření virtuální sítě Azure pro velké instance HANA.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Vytvořit virtuální síť Azure pro velké instance HANA

>[!Note]
>Virtuální síť Azure pro velké instance HANA musí být vytvořené pomocí modelu nasazení Azure Resource Manageru. Řešení pro velké Instance HANA nepodporuje starší model nasazení Azure, obvykle označuje jako model nasazení classic.

Na webu Azure portal, PowerShell, šablony Azure nebo Azure CLI slouží k vytvoření virtuální sítě. (Další informace najdete v tématu [vytvořit virtuální síť pomocí webu Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). V následujícím příkladu se podíváme na virtuální síť, která je vytvořená pomocí webu Azure portal.

Podíváme se, jak souvisí uvádíme jako různých rozsahů IP adres definice virtuální sítě. Když mluvíme o **adresní prostor**, myslíme adresní prostor virtuální sítě Azure může používat. Tento adresní prostor je také požadovaný rozsah adres virtuální sítě používá pro šíření tras BGP. To **adresní prostor** uvedené tady:

![Adresní prostor virtuální sítě Azure zobrazí na webu Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

V předchozím příkladu s 10.16.0.0/16 byl zadán virtuální síť Azure jste spíše velká a široký rozsah IP adres používat. Proto všechny rozsahy IP adres dalších podsítí v rámci této virtuální síti mají jejich rozsahy v rámci tohoto adresního prostoru. Nedoporučujeme ale obvykle takové velký rozsah adres jedné virtuální sítě v Azure. Ale podívejme se na podsítě, které jsou definovány ve virtuální síti Azure:

![Podsítě virtuální sítě Azure a jejich rozsahy IP adres](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Podíváme na virtuální síť s první podsíť virtuálních počítačů (tady se nazývá "Výchozí") a podsítě s názvem "GatewaySubnet".

V následující části označujeme rozsah IP adres podsítě, která se nazývá "Výchozí" v grafiky, jako **rozsah adres IP podsítě virtuálních počítačů Azure**. Budeme odkazovat na rozsah IP adres podsítě brány, jako **rozsah adres IP podsítě brány virtuální sítě**. 

Ve dvou předchozí grafiky **adresní prostor virtuální sítě** se vztahuje **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP podsítě brány virtuální sítě**. 

K zachování nebo být konkrétní rozsahy IP adres, můžete omezit **adresní prostor virtuální sítě** na konkrétní oblasti používané každé podsíti virtuální sítě. Můžete definovat **adresní prostor virtuální sítě** virtuální sítě jako více konkrétních oblastí, jak je znázorněno zde:

![Adresní prostor virtuální sítě Azure s dvě mezery](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

V takovém případě **adresní prostor virtuální sítě** má dvě mezery, které jsou definovány. Jsou stejné jako rozsahy IP adres, které jsou definovány **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP podsítě brány virtuální sítě.**

Můžete použít všechny standardní pojmenování, který rádi používáte pro tyto podsítě tenanta (podsítí virtuálních počítačů). Ale **musí vždy obsahovat jeden a pouze jeden, podsíť brány pro každou virtuální síť** , která se připojuje k SAP HANA v okruhu ExpressRoute Azure (velké instance). A **tuto podsíť brány musí mít název "GatewaySubnet"** abyste měli jistotu, že je správně umístěny bránu ExpressRoute.

> [!WARNING] 
> Je velmi důležité, že podsíť brány vždy být s názvem "GatewaySubnet".

Můžete použít několik podsítí virtuálních počítačů a rozsahy adres nesouvislé. Tyto rozsahy adres musí být pokryté komponentami **adresní prostor virtuální sítě** ve virtuální síti. Je možné v agregované podobě nebo v seznamu přesné rozsahy adres podsítě virtuálních počítačů a podsíť brány.

Dále je uveden seznam důležitých faktů o službě Azure virtual network, která se připojuje k velké instance HANA:

- Musíte odeslat **adresní prostor virtuální sítě** společnosti Microsoft při počátečním nasazení nástroje velkých instancích HANA. 
- **Adresní prostor virtuální sítě** může být jeden větší rozsah, které pokrývá rozsah pro rozsah adres rozsahy adres IP podsíť virtuálních počítačů Azure a podsíť IP brány virtuální sítě.
- Nebo můžete odeslat více oblastí, které se týkají různých IP adresa rozsahy rozsahy adres IP podsíť virtuálních počítačů a rozsah adres IP brány virtuální sítě.
- Určené **adresní prostor virtuální sítě** se používá pro rozšíření směrování protokolu BGP.
- Název podsítě brány musí být: **"GatewaySubnet"**.
- Adresní prostor se používá jako filtr na straně velká Instance HANA povolit nebo zakázat provoz s jednotkami velká Instance HANA v Azure. Ujistěte se, že protokol BGP, směrování informace o Azure virtual network a rozsahy IP adres, které jsou nakonfigurované pro filtrování na straně shodu velká Instance HANA. V opačném případě může dojít problémům s připojením.
- Existují některé podrobnosti o podsíti brány, které jsou popsané později, v části **propojení virtuální sítě pro ExpressRoute velké Instance HANA.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Chcete-li definovat různé rozsahy IP adres 

Představili už některé rozsahy IP adres, které jsou nezbytné pro nasazení velkých instancích HANA. Ale existují další rozsahy IP adres, které jsou také důležité. Podívejme se některé další podrobnosti. Následující IP adresy není nutné k odeslání společnosti Microsoft. Však nutné definovat před odesláním požadavku pro počáteční nasazení:

- **Adresní prostor virtuální sítě**: zavedeném dříve, **adresní prostor virtuální sítě** je rozsah IP adres (nebo oblastí), že jste přiřadil/a (nebo plánujete přiřadit) vaše adresa místo parametru v Azure virtual sítě, která připojení k prostředí velkou instanci SAP HANA.

 Doporučujeme vám, že tento parametr místo adresy je Víceřádkový hodnotu sestává z rozsahy podsítě virtuálních počítačů Azure a rozsahu podsítě brány Azure, jak je znázorněno v předchozí grafiky. Tento rozsah musí nepřekrývá s vaší on-premises nebo server fond IP adres nebo rozsahy adres ER P2P. 
 
Jak získat tyto rozsahy IP adres? 

Vaší podnikové síti týmu nebo poskytovali služeb, musí zadat jeden nebo více IP adresu, rozsah(y), které nejsou použity uvnitř vaší sítě. Například pokud je 10.0.1.0/24 podsítě virtuálních počítačů Azure a podsíť brány Azure 10.0.2.0/28, pak doporučujeme, adresní prostor vaší virtuální síti Azure má dva řádky: 10.0.1.0/24 a 10.0.2.0/28. 

I když hodnoty místo adres se dají agregovat, doporučujeme, abyste zjišťování shody s rozsahy adres podsítě. Tímto způsobem vyhnete náhodně znovu využijete nevyužité rozsahy IP adres v rámci větší adresních prostorech v budoucnu jinde v síti. **Adresní prostor virtuální sítě je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když požádáte pro počáteční nasazení**.

- **Rozsah IP adres Azure VM podsítě:** tuto IP adresu rozsahu, jak je uvedeno výše, je ten přiřadíte (nebo plán přiřazení) pro parametr podsítě virtuální sítě Azure ve službě Azure virtual network, který se připojuje k velké Instance SAP HANA prostředí. Tento rozsah IP adres se používá k přiřazování IP adres pro virtuální počítače Azure. IP adresy mimo tento rozsah mohou připojit na vašich serverech velké Instance SAP HANA. V případě potřeby můžete použít několik podsítí virtuálních počítačů Azure. Doporučujeme, abyste/24 je blok CIDR pro každou podsíť virtuálních počítačů Azure. Tento rozsah adres musí být součástí hodnoty, které se používají v adresním prostoru virtuální sítě Azure. 

Jak získáte tento rozsah IP adres 

Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, který se nepoužívá uvnitř vaší sítě.

- **Rozsah adres IP podsítě brány virtuální sítě:** v závislosti na funkcích, které chcete použít, doporučená velikost je:
   - Bránu ExpressRoute úrovně Ultra-performance: / 26 blok adres--požadované pro SKU typu II třídu.
   - Koexistence pomocí sítě VPN nebo ExpressRoute přes bránu virtuální sítě ExpressRoute výkonné (nebo menší): / 27 blok adres.
   - Všechny ostatní případy: / 28 blok adres. Tento rozsah adres musí být součástí hodnot použitých v hodnotách "Adresní prostor virtuální sítě". Tento rozsah adres musí být součástí hodnoty, které se používají hodnoty místo adres virtuální sítě Azure, které odesílají do Microsoftu. Jak získáte tento rozsah IP adres Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, který není aktuálně používán ve vaší síti. 

- **Rozsah pro připojení k ER P2P adres:** tento rozsah je rozsah IP pro připojení k SAP HANA velké Instance ExpressRoute (ER) P2P. Tento rozsah IP adres musí být minimální velikostí/29 rozsah CIDR IP adres. Tento rozsah musí nepřekrývá s místním nebo dalších IP adres Azure rozsahy adres. Tento rozsah IP adres se používá k nastavení připojení ER ze brána ExpressRoute virtuálních serverech velké Instance SAP HANA. Jak získáte tento rozsah IP adres Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, který není aktuálně používán ve vaší síti. **Tento rozsah je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když s výzvou k počátečnímu nasazení**.
  
- **Rozsah adres fondu IP serveru:** jednotlivé IP adresu přiřadit k serverům velká instance HANA se používá tato IP adresa rozsahu. Velikost doporučená podsítě je/24 je blok CIDR. V případě potřeby může být menší, s pouhými 64 IP adresami. Z tohoto rozsahu prvních 30 IP adresy jsou vyhrazené pro použití společností Microsoft. Ujistěte se, že při výběru velikosti oblasti účtu pro tuto skutečnost. Tento rozsah musí nepřekrývá s místním nebo dalších IP adres Azure adresy. Jak získáte tento rozsah IP adres Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, který není aktuálně používán ve vaší síti. / 24 (doporučeno) jedinečné blok CIDR pro přiřazení konkrétních IP adres je potřeba pro SAP HANA v Azure (velké instance). **Tento rozsah je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když s výzvou k počátečnímu nasazení**.
 
I když je potřeba definovat a plánování rozsahů IP adres, které bylo popsáno dříve, není nutné přenášet všechny z nich společnosti Microsoft. Rozsahy IP adres, které je třeba název společnosti Microsoft jsou:

- Prostory adres virtuální sítě Azure
- Rozsah adres pro připojení k ER P2P
- Rozsah adres fondu IP serveru

Pokud chcete přidat další virtuální sítě, které je potřeba připojit k velké instance HANA, budete muset odeslat nový adresní prostor virtuální sítě Azure, který přidáváte do Microsoft. 

Následuje příklad různé rozsahy a některé příklad rozsahy podle musíte konfigurovat a nakonec je poskytnout společnosti Microsoft. Hodnota pro adresní prostor virtuální sítě Azure nejsou agregovány v prvním příkladu, ale je definován v oblasti první IP adresa rozsahu podsítě virtuálních počítačů Azure a rozsah adres IP podsíť brány virtuální sítě. 

Při konfiguraci a odeslat dalších rozsahů IP adres z podsítě další virtuální počítač v rámci adresního prostoru virtuální sítě Azure, můžete použít několik podsítí virtuálních počítačů v rámci virtuální sítě Azure.

![Rozsahy IP adres v SAP HANA vyžaduje minimální nasazení Azure (velké instance)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Můžete také agregovat data, která odešlete do Microsoftu. V takovém případě adresní prostor virtuální sítě Azure zahrnuje jenom jedna mezera. Použití rozsahů IP adres z předchozího příkladu, adresní prostor virtuální sítě agregované by měl vypadat jako na následujícím obrázku:

![Druhá možnost vyžaduje v SAP HANA v Azure (velké instance) minimálního nasazení rozsahy IP adres](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak je vidět v příkladu, místo dvě menší rozsahy definované adresní prostor virtuální sítě Azure, máme jeden větší rozsah, který zahrnuje 4096 IP adresy. Velké definice adresního prostoru znamená některé docela rozsáhlá rozsahy nevyužitá. Vzhledem k tomu, že hodnoty místo adres virtuální sítě se používají pro šíření tras protokolu BGP, využití nevyužité oblasti v místním nebo jinde v síti může způsobit problémů se směrováním. 

Proto doporučujeme, abyste adresní prostor naprosto v souladu s skutečné podsítě adresní prostor, který používáte. V případě potřeby, aniž by došlo k výpadku ve virtuální síti, můžete vždy přidat nové hodnoty místo adres později.
 
> [!IMPORTANT] 
> Každý rozsah adres IP v ER P2P, fond IP adres serveru a adresní prostor virtuální sítě Azure musí **není** překrývat mezi sebou nebo s jinou oblast, která se používá ve vaší síti. Každý musí být diskrétní. Jak ukazují dvě předchozí grafiky, také nemohou být podsíť jinou oblast. Pokud dojde k překrytí mezi oblastmi, virtuální síť Azure nemusí připojit k okruhu ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Další kroky po definovali rozsahy adres
Po definování rozsahy IP adres je potřeba stát následující věci:

1. Odešlete rozsahy IP adres pro adresní prostor virtuální sítě Azure, ER P2P připojení a server fondu rozsah IP adres, společně s další data, která byla uvedena na začátku dokumentu. V tomto okamžiku může také začnete vytvářet virtuální sítě a podsítě virtuálních počítačů. 
2. Okruh ExpressRoute je vytvořen microsoftem mezi vašeho předplatného Azure a razítko velká Instance HANA.
3. Síť klienta se vytvoří na razítku velká Instance společností Microsoft.
4. Microsoft nakonfiguruje sítě v SAP HANA v Azure (velké instance) infrastrukturu tak, aby přijímal IP adresy z adresního prostoru virtuální sítě Azure, který komunikuje s velkých instancích HANA.
5. V závislosti na konkrétní SAP HANA v Azure (velké instance) SKU, které jste koupili Microsoft přiřadí výpočetní jednotka v síti klientů, přiděluje a připojí úložiště a nainstaluje operační systém (SUSE nebo Red Hat Linux). IP adresy pro tyto jednotky se přesunete mimo rozsah adres fondu IP serveru, který jste odeslali do Microsoftu.

Na konci procesu nasazení poskytuje Microsoft vám následující data:
- Informace, které má potřebné pro připojení vašich virtuálních sítí Azure pro okruh ExpressRoute, který se připojuje virtuální sítě Azure pro velké instance HANA:
     - Autorizace klíče
     - ExpressRoute PeerID
- Data pro přístup k velké instance HANA po vytvoření okruhu ExpressRoute a služba Azure virtual network.

Můžete také vyhledat posloupnost připojení velkých instancích HANA v dokumentu [SAP HANA v Azure (velké instance) nastavení](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Mnoho z následujících kroků jsou uvedeny v příkladu nasazení v tomto dokumentu. 

**Další kroky**

- Odkazovat na [propojení virtuální sítě pro ExpressRoute velké Instance HANA](hana-connect-vnet-express-route.md).
