---
title: Infrastruktury a připojení k SAP HANA v Azure (velké instance) | Microsoft Docs
description: Konfigurace infrastruktury požadované připojení používat SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51089ffa05168d2309bd2a96ec44b2ce0fed75f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastruktura SAP HANA (velké instance) a připojení v Azure 

Definice předem před čtením této příručky. V [přehled SAP HANA (velké instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) zavedli jsme dvěma různými třídami velké Instance HANA jednotek s:

- S72, S72m, S144, S144m, S192 a S192m, které označujeme jako typu Type I třídy, z jednotky SKU.
- S384, S384m, S384xm, S576m, S768m a S960m, které označujeme jako typ II třída z jednotky SKU.

Specifikátory třídy se bude používat v dokumentaci velké Instance HANA nakonec odkazovat na různé možnosti a požadavky založené na HANA velké Instance SKU.

Další definice, které jsme používané jsou:
- **Velké Instance razítka:** zásobníku infrastruktury hardwaru, který je SAP HANA TDI certifikaci a vyhrazené pro spuštění instance SAP HANA v rámci Azure.
- **SAP HANA v Azure (velké instance):** oficiální název nabídky v Azure ke spuštění HANA instancí v na SAP HANA TDI certifikaci hardwaru, která je nasazena v velké Instance razítka v různých oblastech Azure. Související termín **HANA velké Instance** je zkratka pro SAP HANA v Azure (velké instance) a je široce používá tato příručka technického nasazení.
 

Po zakoupení SAP HANA v Azure (velké instance) je dokončené mezi vámi a týmu účtů Microsoft enterprise, následující informace se společností Microsoft vyžadovány pro nasazení HANA velké Instance jednotky:

- Jméno zákazníka
- Firemní kontaktní údaje (včetně e-mailová adresa a telefonní číslo)
- Technické kontaktní informace (včetně e-mailová adresa a telefonní číslo)
- Technické síťové kontaktní informace (včetně e-mailová adresa a telefonní číslo)
- Oblast Azure nasazení (západní USA, Východ USA, Austrálie – východ, Austrálie – jihovýchod, západní Evropa a Severní Evropa od července 2017)
- Potvrďte SAP HANA na Azure (velké instance) SKU (konfigurace)
- Jak již popsané v dokumentu přehled a architektura pro velké instance HANA, pro každou oblast Azure, který je nasazován na:
    - / 29 rozsah IP adres pro ER P2P připojení, která propojení virtuálních sítí Azure pro velké instancí HANA
    - / 24 používá blok CIDR pro fondu IP HANA velké instance serveru
- Hodnoty rozsahu IP adres použité v atribut adresní prostor sítě VNet každý virtuální síť Azure, která se připojuje k velké instancí HANA
- Data pro každou HANA velké instancí systému:
  - Požadované hostname - ideálně s plně kvalifikovaný název domény.
  - Požadovanou IP adresu pro jednotku velké Instance HANA mimo rozsah adres fondu IP serverů - mějte na paměti, že prvních 30 IP adresy v rozsahu Server fond IP adres jsou vyhrazené pro interní použití v rámci instancí velké HANA
  - SAP HANA SID název instance SAP HANA (nutné vytvářet svazky potřeby související SAP HANA disku). Identifikátor SID HANA je nezbytné k vytváření oprávnění pro sidadm na svazky systému souborů NFS, které jsou získávání připojené k jednotce HANA velké Instance. Také je používán jako jedna z komponent název disku svazků, které získat připojené. Pokud chcete spustit více než jednu instanci HANA na jednotce, budete muset seznam identifikátorů SID více HANA. Každé z nich získá samostatnou sadu svazky, které jsou přiřazeny.
  - K vytvoření svazky potřeby související SAP HANA disku je potřeba groupid, které má uživatel sidadm v operačním systému Linux. Instalace SAP HANA obvykle vytvoří skupinu sapsys s id skupiny 1001. Sidadm uživatel je součástí této skupiny
  - ID uživatele, který má uživatel sidadm v operačním systému Linux, je potřeba vytvářet svazky potřeby související SAP HANA disku. Pokud používáte více instancí HANA na jednotce, budete muset seznam všech <sid>uživatelé adm 
- ID předplatného Azure pro předplatné Azure, na které SAP HANA na Azure HANA velké instance má být přímo připojen. Toto ID předplatného odkazuje předplatné Azure, který bude účtován jednotka HANA velké Instance.

Po zadání informací Microsoft zřídí SAP HANA v Azure (velké instance) a vrátí informace nezbytné propojení sítě Azure Vnet s HANA velké instancí a přístup k instanci HANA velké jednotky.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Připojování k velké instancí HANA virtuální počítače Azure

Jak již bylo uvedeno v [přehled SAP HANA (velké instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) minimální nasazení velké instancí HANA s aplikační vrstvu SAP v Azure vypadá jako:

![Virtuální síť Azure připojené k SAP HANA na Azure (velké instance) a na místě](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Vyhledávání blíže na straně virtuální síť Azure, Uvědomujeme potřeba:

- Definice o virtuální síť, která se má použít k nasazení virtuálních počítačů ve vrstvě aplikace SAP do Azure.
- Které automaticky znamená, že výchozí podsíť ve virtuální síti Azure je definován, je ve skutečnosti používaný k nasazení virtuálních počítačů do.
- Virtuální síť Azure, který se vytvoří musí mít alespoň jednu podsíť virtuálních počítačů a jednu podsíť brány ExpressRoute. Tyto podsítě by se měla přiřadit rozsahy IP adres jako zadaný a popsaných v následujících částech.

Ano Podíváme se poněkud blíže do vytvoření virtuální sítě Azure pro velké instance HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Vytvoření virtuální sítí Azure pro velké instance HANA

>[!Note]
>Virtuální síť Azure pro velké instanci HANA musí být vytvořen pomocí modelu nasazení Azure Resource Manager. Řešení HANA velké Instance nepodporuje starší model nasazení Azure, obvykle označuje jako model nasazení classic.

Virtuální sítě můžete vytvořit pomocí portálu Azure, PowerShell, šablony Azure nebo rozhraní příkazového řádku Azure (viz [vytvoření virtuální sítě pomocí portálu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). V následujícím příkladu se podíváme do virtuální sítě vytvořené pomocí portálu Azure.

Pokud jsme viděl definice o virtuální síť Azure prostřednictvím portálu Azure, podíváme se na některé z definice a jak těch, které se týkají jsme seznam rozsahů adres různých IP. Jak jsme mluvíme o **adresní prostor**, jsme znamenat adresního prostoru, který může použít virtuální síť Azure. Tento adresní prostor je také rozsah adres, virtuální sítě používá pro šíření trasy protokolu BGP. To **adresní prostor** můžete zobrazit tady:

![Adresa místa Azure VNet zobrazí na portálu Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

V případě, že před s 10.16.0.0/16 byl zadán virtuální síť Azure místo velké a široké rozsah IP adres používat. Znamená, že všechny rozsahy IP adres z dalších podsítí v rámci této virtuální sítě může mít jejich rozsahů v rámci tohoto adresního prostoru. Obvykle nejsou doporučujeme rozsah velkých adres pro jednu virtuální síť v Azure. Ale získání další krok, podíváme se do podsítí definovaný ve virtuální síti Azure:

![Azure podsítě virtuální sítě a jejich rozsahy IP adres](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Jak vidíte, podíváme na virtuální síť s první podsíť virtuálních počítačů (zde nazývané "default") a podsíť s názvem "GatewaySubnet".
V následující části, označujeme rozsah IP adres podsítě, která se nazývá "default" grafického jako **rozsah adres IP podsítě virtuálních počítačů Azure**. V následujících částech, označujeme rozsah IP adres podsítě brány jako **rozsah adres IP adresu podsítě brány virtuální sítě**. 

V případě znázorněno pomocí dvou grafika výše, uvidíte, že **adresní prostor sítě VNet** popisuje, jak **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP adresu podsítě brány virtuální sítě**. 

V ostatních případech, kdy potřebujete šetří nebo být konkrétní rozsahy IP adres, můžete omezit **adresní prostor sítě VNet** sítě vnet na konkrétní oblasti používá každá podsíť. V takovém případě můžete definovat **adresní prostor sítě VNet** z virtuální sítě jako více konkrétní rozsahy, jak je vidět tady:

![Azure adresní prostor sítě VNet dva prostory](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

V takovém případě **adresní prostor sítě VNet** má dva prostory definované. Tyto dva prostory jsou shodné s rozsahy IP adres, které jsou definovány pro **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP adresu podsítě brány virtuální sítě.**

Můžete použít všechny standardní pojmenování, které chcete pro tyto podsítě klienta (podsítě virtuálních počítačů). Ale **musí být vždy jedna a pouze jedna podsíť brány pro každý virtuální síť** která se připojuje k SAP HANA v okruhu ExpressRoute Azure (velké instance). A **tuto podsíť brány musí vždy mít název "GatewaySubnet"** zajistit správné umístění bránu ExpressRoute.

> [!WARNING] 
> Je důležité, aby podsíť brány vždy je s názvem "GatewaySubnet".

Může použít více podsítí virtuálních počítačů i využitím rozsahy nesouvislé adres. Jak je uvedeno nahoře, tyto rozsahy adres musí být předmětem, ale **adresní prostor sítě VNet** sítě vnet v souhrnné podobě nebo v seznamu rozsahů přesný podsítě virtuálních počítačů a podsíť brány.

Shrnutí důležitých faktů o virtuální síť Azure, která se připojuje k HANA velké instancí:

- Budete muset odeslat společnosti Microsoft **adresní prostor sítě VNet** při provádění je počáteční nasazení HANA velké instancí. 
- **Adresní prostor sítě VNet** může být jeden větší rozsah, který pokrývá rozsah pro virtuální počítač Azure podsítě IP adres rozsahy a rozsah adres IP adresu podsítě brány virtuální sítě.
- Nebo můžete odeslat jako **adresní prostor sítě VNet** více rozsahů, které se týkají různých IP adres, rozsahů rozsahy adres IP podsíť virtuálních počítačů a rozsah adres IP adresu podsítě brány virtuální sítě.
- Definovaný **adresní prostor sítě VNet** je použité šíření směrování protokolu BGP.
- Název podsítě brány musí být: **"GatewaySubnet".**
- **Adresní prostor sítě VNet** slouží jako filtr na straně velké Instance HANA povolit nebo zakázat provoz s jednotkami velké Instance HANA z Azure. Pokud se informace o směrování protokolu BGP virtuální síť Azure a rozsahů IP adres, které jsou nakonfigurované pro filtrování na straně HANA velké Instance neshodují, mohou nastat problémy v připojení.
- Existují některé podrobnosti o podsíť brány, které jsou popsané níže v části "propojení virtuální sítě HANA velké Instance ExpressRoute.



### <a name="different-ip-address-ranges-to-be-defined"></a>Různých rozsahů IP adres, který se má definovat 

Již zavedli jsme některé z rozsahů IP adres nezbytné pro nasazení HANA velké instancí v předchozích částech. Ale existují některé další rozsahy IP adres, které jsou důležité. Přejděte prostřednictvím některé další podrobnosti. Následující adresy IP, ve kterých není nutné odeslat společnosti Microsoft je potřeba určit, před odesláním požadavku pro počáteční nasazení:

- **Virtuální síť adresní prostor:** už zavedená dříve, nebo jsou IP adresy range(s) jste přiřadili (nebo plán přiřadit) vaší parametr místo adresy ve virtuální sítě Azure (VNet) připojení k prostředí SAP HANA velké Instance. Doporučuje se, že tento parametr adresní prostor je hodnota Víceřádkový skládá z rozsahy podsíť virtuálních počítačů Azure a rozsahu podsítě brány Azure, jak je znázorněno grafiky dříve. Tento rozsah musí nepřekrývají s místními nebo rozsahů adres IP fondu serverů nebo ER P2P. Jak získat je, že tyto rozsahy IP adres? Poskytovatel tým nebo služby podnikové síti by měl poskytovat jeden nebo více IP adres rozsahy, které nebo nejsou použity uvnitř vaší sítě. Příklad: Pokud je vaše podsíť virtuálních počítačů Azure (viz výše) 10.0.1.0/24 a podsítě brány Azure (viz následující) je 10.0.2.0/28, pak váš adresní prostor virtuální sítě Azure se doporučuje se dva řádky; 10.0.1.0/24 a 10.0.2.0/28. I když se dají agregovat hodnoty adresní prostor, se doporučuje odpovídající je na rozsahy podsíť předejdete náhodnému opakované použití nepoužívané rozsahy IP adres v rámci větší adresní prostory v budoucnu jinde v síti. **Virtuální síť adresní prostor je rozsah IP adres, který se musí k odeslání společnosti Microsoft při požadující počáteční nasazení**

- **Azure rozsah adres IP podsítě virtuálních počítačů:** tento adresy IP, jak je popsáno dříve již je ten, který jste přiřadili (nebo plán přiřazení) parametr podsíť virtuální sítě Azure ve vaší virtuální síti Azure připojení k prostředí SAP HANA velké Instance. Tento rozsah adres IP se používá k přidělování IP adres pro virtuální počítače Azure. IP adresy mimo tento rozsah mohou připojit k vaší instanci velké SAP HANA servery. V případě potřeby lze několik podsítí virtuálních počítačů Azure. A/24 blok CIDR se společnost Microsoft doporučuje pro každou podsíť virtuálních počítačů Azure. Tento rozsah adres musí být součástí hodnoty používané v adresním prostoru virtuální sítě Azure. Jak získat tento rozsah IP adres? Poskytovatel tým nebo služby podnikové síti by měl poskytovat rozsah IP adres, který není právě používána uvnitř vaší sítě.

- **Rozsah adres IP adresu podsítě brány virtuální sítě:** v závislosti na funkce, kterou plánujete použít, bude doporučená velikost:
   - Brány úrovně Ultra-performance ExpressRoute: / 26 blok adres - vyžaduje pro typ II třídu SKU
   - Koexistenci se připojení VPN a ExpressRoute pomocí vysoce výkonné brány ExpressRoute (nebo menší): / 27 blok adres
   - Další situace: / 28 blok adres. Tento rozsah adres musí být součástí hodnoty používané v hodnoty "Adresní prostor virtuální sítě". Tento rozsah adres musí být součástí hodnoty používané v adresní prostor sítě VNet Azure hodnoty, které budete muset odeslat společnosti Microsoft. Jak získat tento rozsah IP adres? Poskytovatel tým nebo služby podnikové síti by měl poskytovat rozsah IP adres, který není právě používána uvnitř vaší sítě. 

- **Rozsah pro připojení k síti ER P2P adres:** tento rozsah je rozsah IP adres pro připojení k SAP HANA velké Instance ExpressRoute (ER) P2P. Tento rozsah IP adres musí být/29 rozsah CIDR IP adres. Tento rozsah musí nepřekrývají s vaší místní nebo jiné IP Azure rozsahy adres. Tento rozsah adres IP se používá k nastavení připojení ER z bránu ExpressRoute virtuální síť Azure pro velké Instance SAP HANA servery. Jak získat tento rozsah IP adres? Poskytovatel tým nebo služby podnikové síti by měl poskytovat rozsah IP adres, který není právě používána uvnitř vaší sítě. **Tento rozsah je rozsah IP adres, který se musí k odeslání společnosti Microsoft při požadující počáteční nasazení**
  
- **Rozsah adres fondu IP serveru:** tento rozsah adres IP se používá k jednotlivé IP adresu přiřadit HANA velké instance serverů. / 24 je velikost doporučené podsítě CIDR blokovat – ale pokud potřeby může být menší na minimum poskytnout 64 IP adresy. Z tohoto rozsahu prvních 30 IP adresy jsou vyhrazené pro použití společností Microsoft. Zkontrolujte, zda že je tento fakt pozornost při výběru velikost rozsahu. Tento rozsah musí nepřekrývají s vaší místní nebo jiné Azure IP adresy. Jak získat tento rozsah IP adres? Poskytovatel tým nebo služby podnikové síti by měl poskytovat rozsah IP adres, který není právě používána uvnitř vaší sítě. / 24 (doporučeno) jedinečný CIDR blokovat má být použit pro přiřazení konkrétní IP adresy, které jsou potřebné pro SAP HANA v Azure (velké instance). **Tento rozsah je rozsah IP adres, který se musí k odeslání společnosti Microsoft při požadující počáteční nasazení**
 
Když potřebujete definovat a plánování rozsahů IP adres výše, ne všechny je třeba přenášet do společnosti Microsoft. To Shrneme výše, rozsahy IP adres, které jsou třeba název společnosti Microsoft jsou:

- Azure VNet adresu mezerou (mezerami)
- Rozsah adres pro připojení k síti ER P2P
- Rozsah adres fondu IP serveru

Přidání dalších virtuálních sítí, potřebujete pro připojení k instancím velké HANA, vyžaduje, abyste odeslání nové Azure VNet adresní prostor přidáváte do Microsoft. 

Tady je příklad různých rozsahů a rozsahů některé příklad, jak je budete muset nakonfigurovat a nakonec zadejte společnosti Microsoft. Jak vidíte, hodnota adresní prostor virtuální sítě Azure nejsou agregovány v prvním příkladu, není však definována z rozsahů první IP adresa rozsahu podsítě virtuálních počítačů Azure a rozsah adres IP adresu podsítě brány virtuální sítě. Použití více podsítí virtuálních počítačů v rámci virtuální sítě Azure bude fungovat, odpovídajícím způsobem pomocí konfigurace a odesláním dalších IP adres rozsahy další podsítí virtuálních počítačů v rámci adresního prostoru virtuální sítě Azure.

![Rozsahy IP adres na minimální nasazení Azure (velké instance) vyžadují v SAP HANA](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Máte také možnost agregování dat, která můžete odeslat společnosti Microsoft. V takovém případě adresní prostor virtuální sítě Azure by obsahovat jenom jeden prostor. Použití rozsahů IP adres používaný v příkladu dříve. Tento agregované virtuální síť adresní prostor by měl vypadat jako:

![Druhá možnost rozsahů IP adres na minimální nasazení Azure (velké instance) vyžadují v SAP HANA](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak je uvedeno výše, místo dvou menší rozsahy, které definované adresní prostor sítě vnet Azure, máme jeden větší rozsah, které pokrývá 4096 IP adresy. Velké definice adresního prostoru zanechává některé místo velkých oblastí nepoužívá. Vzhledem k tomu, že hodnoty adresní prostor sítě VNet se používají pro šíření trasy protokolu BGP, využití nepoužívané rozsahy místní nebo jinde v síti může způsobit problémy s směrování. Proto se doporučuje udržovat adresní prostor naprosto v souladu s adresního prostoru skutečné podsítě použít. V případě potřeby, aniž by docházelo k výpadkům ve virtuální síti, můžete vždy přidat nové hodnoty adresní prostor později.
 
> [!IMPORTANT] 
> Každou IP adresu adresní prostor sítě VNet Azure rozsah ER – P2P, IP fondu serverů, musí **není** navzájem překrývají nebo jiné rozsahu použít jinde v síti; každý musí být diskrétní, a jako dvou grafika starší zobrazit, nemusí být podsíť jiných rozsahu. Dojde-li překrytí mezi rozsahy, virtuální síť Azure nemusí připojit k okruhu ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Další kroky po nebyly definovány rozsahy adres
Po definování rozsahy IP adres je potřeba dojít níže uvedených aktivit:

1. Odešlete rozsahy IP adres pro adresní prostor sítě VNet Azure, ER P2P připojení a rozsah adres fondu IP serveru, spolu s další data, která je uveden na začátku dokumentu. V tuto chvíli můžete také může spustit pro vytvoření sítě VNet a podsítě virtuálních počítačů. 
2. Okruh Express Route se vytvoří Microsoft mezi vašeho předplatného Azure a razítko HANA velké Instance.
3. Síť klienta se vytvoří na velké Instance razítka společností Microsoft.
4. Microsoft nakonfiguruje sítě v SAP HANA na infrastrukturu Azure (velké instance) tak, aby přijímal IP adresy z Azure VNet adresního prostoru komunikující s instancemi velké HANA.
5. V závislosti na konkrétní SAP HANA na Azure (velké instance) SKU zakoupili Microsoft přiřadí výpočetní jednotka v síti klientů, přidělit a připojte úložiště a instalace operačního systému (SUSE nebo Red Hat Linux). IP adresy pro tyto jednotky se vyjímají z Server fond IP adres rozsah odeslané společnosti Microsoft.

Na konci procesu nasazení poskytuje Microsoft vám následující data:
- Informace potřebné pro připojení vaší VNet(s) Azure pro okruh ExpressRoute, která se připojuje k HANA velké instance virtuálních sítí Azure:
     - Autorizace klíče
     - ExpressRoute PeerID
- Data pro přístup k velké instancí HANA po vytvoření okruhu ExpressRoute a virtuální síť Azure.

Můžete také vyhledat pořadí propojení HANA velké instancí v dokumentu [koncová instalační program pro velké instance SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Mnoho z těchto kroků jsou uvedeny v ukázkové nasazení v tomto dokumentu. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Propojení virtuální sítě HANA velké Instance ExpressRoute

Jako definovaný všechny rozsahy IP adres a nyní zpět data přijata od společnosti Microsoft, můžete začít připojení virtuální sítě můžete vytvořit před na velké instance HANA. Po vytvoření virtuální sítě Azure bránu ExpressRoute musí být vytvořeny na virtuální síť k propojení virtuální sítě k okruhu ExpressRoute, která se připojuje k zákazníka klienta s časovým razítkem velké Instance.

> [!NOTE] 
> Tento krok může trvat až 30 minut dokončit, protože nové bráně se vytvoří v určené předplatného Azure a pak připojené k zadané virtuální síť Azure.

Pokud brána už existuje, zkontrolujte, zda je bránu ExpressRoute nebo ne. Pokud ne, brána musí být odstraněn a znovu vytvořen jako bránu ExpressRoute. Pokud bránu ExpressRoute je už vytvořené, protože virtuální síť Azure je již připojen k okruhu ExpressRoute pro připojení k místní síti, pokračujte v níže uvedené části propojování virtuálních sítí.

- Použijte buď (nové) [portál Azure](https://portal.azure.com/), nebo prostředí PowerShell k vytvoření brány ExpressRoute VPN připojen k virtuální síti.
  - Pokud používáte portál Azure, přidejte nový **brány virtuální sítě** a pak vyberte **ExpressRoute** jako typ brány.
  - Pokud jste zvolili namísto toho prostředí PowerShell, nejprve stáhnout a použít nejnovější [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) zajistit optimální zkušenosti. Následující příkazy vytvořte bránu ExpressRoute. Texty před sebou _$_ jsou proměnné definované uživatelem, které je třeba aktualizovat pomocí vaší konkrétní informace.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

V tomto příkladu se použila HighPerformance SKU brány. Vaše možnosti jsou HighPerformance nebo UltraPerformance jako bránu jenom jednotky SKU, které jsou podporovány pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instancí HANA verze SKU typy S384, S384m, S384xm, S576m, S768m a S960m (SKU – třída typu II), využití UltraPerformance SKU brány je povinné.

### <a name="linking-vnets"></a>Propojení virtuální sítě

Nyní, když virtuální síť Azure bránu ExpressRoute, použijete od společnosti Microsoft informace o ověření pro připojení k SAP HANA na okruh Azure ExpressRoute (velké instance) pro toto připojení vytvořit bránu ExpressRoute. Tento krok můžete provést pomocí portálu Azure nebo Powershellu. Doporučuje se portálu, ale jsou následující pokyny pro PowerShell. 

- Můžete spustit následující příkazy pro každou bránu virtuální sítě pomocí různých AuthGUID pro každé připojení. První dvě položky zobrazené v následující skript, pochází z informace od společnosti Microsoft. Navíc AuthGUID je specifický pro každý virtuální sítě a jeho brány. Znamená, pokud chcete přidat jiné virtuální síti Azure, potřebujete získat další AuthID pro váš okruh ExpressRoute, který se připojuje HANA velké instance do Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Pokud se chcete připojit bránu a více okruhů ExpressRoute, které jsou spojeny s předplatným, musíte provést tento krok více než jednou. Například pravděpodobně chcete připojit bránu virtuální sítě k okruhu ExpressRoute, které virtuální sítě se připojí k místní síti.

## <a name="adding-more-ip-addresses-or-subnets"></a>Přidání další IP adresy nebo podsítě

Použijte portál Azure, prostředí PowerShell nebo rozhraní příkazového řádku při přidávání více IP adres nebo podsítě.

Doporučuje se v takovém případě se k přidání nového rozsahu IP adres jako nový rozsah adresní prostor sítě VNet, místo aby generovala nový agregované rozsah. V obou případech budete muset odeslat tuto změnu společnosti Microsoft, které umožňují připojení z tohoto nového rozsahu IP adres s jednotkami HANA velké Instance v vašeho klienta. Můžete otevřít žádost podporu Azure získat novou virtuální síť adresní prostor přidat. Jakmile se zobrazí potvrzení, proveďte další kroky.

Chcete-li vytvořit další podsítě na portálu Azure, najdete v článku [vytvoření virtuální sítě pomocí portálu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)a pokud chcete vytvořit z prostředí PowerShell, najdete v části [vytvoření virtuální sítě pomocí prostředí PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Přidávání virtuálních sítí

Po počátečním připojení jeden nebo více virtuálních sítí Azure, můžete přidat další ty, které přístup k SAP HANA v Azure (velké instance). Nejprve odeslání žádosti o podporu Azure, v této žádosti současně obsahovat konkrétní informace identifikující konkrétní nasazení Azure a rozsahy IP adres místo adresního prostoru virtuální sítě Azure. SAP HANA na Azure Service Management pak poskytuje potřebné informace, že potřebujete připojit další virtuální sítě a ExpressRoute. Pro každý virtuální síť musíte jedinečný klíč autorizaci k připojení k okruhu ExpressRoute HANA velké instancí.

Postup pro přidání nové virtuální sítě Azure:

1. Dokončení najdete v části prvním krokem v procesu registrace _vytváření virtuální sítě Azure_ části.
2. Dokončení najdete v části druhým krokem v procesu registrace _vytváření podsítě brány_ části.
3. Pokud chcete připojit vaše další virtuální sítě k okruhu ExpressRoute Instance velké HANA, otevřete žádost o podporu Azure s informacemi o nové sítě VNet a požádat o nový klíč autorizace.
4. Po upozornění, že je autorizaci dokončení, použijte informace o ověření poskytovaný společností Microsoft k dokončení třetí krok v procesu registrace najdete _propojování virtuálních sítí_ části.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Zvýšení šířku pásma okruhu ExpressRoute

Poraďte se s SAP HANA na Správa služby Azure. Pokud je doporučeno zvětšit šířku pásma SAP HANA v okruhu ExpressRoute Azure (velké instance), vytvoření žádosti o podporu Azure. (Můžete požádat o zvýšení pro jeden okruh šířky pásma maximálně 10 GB/s.) Pak se zobrazí oznámení po dokončení; žádná další akce potřebnými k povolení Tato vyšší rychlost v Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Přidání další okruhu ExpressRoute

Poraďte se s SAP HANA na Azure Service Management, pokud je doporučeno, že je potřeba další okruh ExpressRoute, ujistěte se, Azure podpory žádosti o vytvoření nové okruhu ExpressRoute (a získat informace o autorizaci k připojení k tomuto). Adresní prostor, který se bude používat na virtuální sítě musí být definován před provedením požadavku, aby SAP HANA na Azure Service Management k poskytování autorizace.

Jakmile se vytvoří nový okruhu a SAP HANA na Azure Service Management configuration se dokončí, budete dostávat oznámení s informací, že budete muset pokračovat. Postupujte podle kroků uvedených výše pro vytváření a propojíte tento okruh další nové sítě VNet. Nemůžete se připojit k tento okruh další virtuální sítě Azure, pokud je již připojen k jiné SAP HANA v okruhu ExpressRoute Azure (velké Instance) ve stejné oblasti Azure.

## <a name="deleting-a-subnet"></a>Odstraňování podsíť

Odebrat podsíť virtuální sítě, můžete použít portál Azure, PowerShell nebo rozhraní příkazového řádku. V případě, že vaše Azure VNet IP adresa rozsahu nebo Azure adresní prostor sítě VNet byla agregované rozsahu, neexistuje žádný postupujte podle službu jste se společností Microsoft. Kromě toho, že virtuální sítě je stále šíření BGP trasy adresní prostor, který zahrnuje odstraněné podsítě. Pokud jste definovali Azure VNet IP adresa rozsahu nebo Azure adresní prostor sítě VNet jako více rozsahů IP adres, z nichž jeden byl přiřazen odstraněné podsíť, by měl odstranit, mimo váš adresní prostor sítě VNet a následně informujte SAP HANA na Azure Service Management jeho odebrání z rozsahů, které může komunikovat s SAP HANA v Azure (velké instance).

Chcete-li odstranit podsíť, přečtěte si téma [odstranit podsíť](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) Další informace o vytvoření podsítě.

## <a name="deleting-a-vnet"></a>Odstranění virtuální sítě

Chcete-li odstranit virtuální síť, přečtěte si téma [odstranit virtuální síť](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA na Azure Service Management Odebere existující autorizací na SAP HANA na okruh Azure ExpressRoute (velké instance) a odeberte Azure VNet IP adresa rozsahu nebo Azure adresní prostor sítě VNet pro komunikaci s instancí velké HANA.

Po odebrání sítě VNet, otevřete žádost podporu Azure k poskytování rozsahy IP adres místo odeberou.

K zajištění, že všechno, co je odebrat, odstraňte následující položky:

- Připojení ExpressRoute, brány virtuální sítě, veřejnou IP adresu brány virtuální sítě a virtuální sítí VNet

## <a name="deleting-an-expressroute-circuit"></a>Odstraňování okruhu ExpressRoute

Odebrat další SAP HANA v okruhu ExpressRoute Azure (velké instance), otevřete žádost o podporu Azure s SAP HANA na Azure Service Management a požadavku, že je okruh, měla by být odstraněna. V rámci předplatného Azure můžete odstranit nebo ponechat virtuální síť podle potřeby. Nicméně je nutné odstranit připojení mezi okruh ExpressRoute velké instancí HANA a propojené brány virtuální sítě.

Pokud chcete odebrat virtuální sítě, postupujte podle pokynů na odstranění virtuální sítě v části výše.


