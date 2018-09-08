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
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164713"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Připojení virtuálních počítačů Azure pro velké instance HANA

Jak již bylo uvedeno v [architektura v Azure a SAP HANA (velké instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) minimálního nasazení velkých instancích HANA s aplikační vrstvě SAP v Azure vypadá jako:

![Virtuální síť Azure připojení k SAP HANA v Azure (velké instance) a místní](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Hledání blíže na straně Azure VNet, Uvědomujeme potřeba:

- Definice virtuální síť, která se bude používat k nasazení virtuálních počítačů ve vrstvě aplikace SAP do Azure.
- Že automaticky znamená, že výchozí podsíť ve virtuální síti Azure je definována, který je ve skutečnosti použitým k nasazení virtuálních počítačů do.
- Virtuální síť Azure, který je vytvořen musí mít alespoň jednu podsíť virtuálních počítačů a jednu podsíť pro bránu ExpressRoute. Tyto podsítě měla být přiřazena rozsahy IP adres jako zadaný a popsané v následujících částech.

Ano Podívejme se trochu blíže do vytvoření virtuální sítě Azure pro velké instance HANA

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Vytvoření virtuální sítě Azure pro velké instance HANA

>[!Note]
>Virtuální síť Azure pro velké Instance HANA je nutné vytvořit pomocí modelu nasazení Azure Resource Manageru. Řešení pro velké Instance HANA nepodporuje starší model nasazení Azure, obvykle označuje jako model nasazení classic.

Virtuální síť můžete vytvořit pomocí webu Azure portal, PowerShell, šablony Azure nebo rozhraní příkazového řádku Azure (viz [vytvořit virtuální síť pomocí webu Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). V následujícím příkladu se podíváme do virtuální sítě vytvořené pomocí webu Azure portal.

Když se podíváme na definice virtuální síť Azure na webu Azure portal, Pojďme se podívat na některé z definice a ty vztah jsme seznam rozsahů adres jinou IP Adresou. Jak se myslí **adresní prostor**, myslíme adresní prostor virtuální sítě Azure může používat. Tento adresní prostor je také požadovaný rozsah adres virtuální sítě používá pro šíření tras BGP. To **adresní prostor** uvedené tady:

![Adresa místa z virtuální sítě Azure zobrazí na webu Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

V případě předchozího s 10.16.0.0/16 byl zadán virtuální sítě Azure jste spíše velká a široký rozsah IP adres používat. Znamená, že všechny rozsahy IP adres z dalších podsítí v rámci této virtuální sítě můžete mít svoje rozsahů v rámci tohoto adresního prostoru. Obvykle jsme nejsou doporučující velké adresu rozsahu pro jednu virtuální síť v Azure. Ale získávání o krok dál, Pojďme se podívat do podsítí definovaných ve virtuální síti Azure:

![Azure podsítě virtuální sítě a jejich rozsahy IP adres](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Jak je vidět, podíváme na virtuální síť s první podsíť virtuálních počítačů (zde označované jako "default") a podsítě s názvem "GatewaySubnet".
V následující části označujeme rozsah IP adres podsítě, která byla volána "default" v grafickém jako **rozsah adres IP podsítě virtuálních počítačů Azure**. V následujících částech označujeme rozsah IP adres podsítě brány jako **rozsah adres IP podsíť brány virtuální sítě**. 

V tomto případě jsme vám ukázali výše uvedené dva grafikou, uvidíte, že **adresní prostor virtuální sítě** zahrnuje obě, **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP podsíť brány virtuální sítě**. 

V ostatních případech, kdy potřebujete šetřit nebo být konkrétní rozsahy IP adres, můžete omezit **adresní prostor virtuální sítě** virtuální sítě na konkrétní oblasti používá v každé podsíti. V takovém případě můžete definovat **adresní prostor virtuální sítě** z virtuální sítě jako více konkrétní rozsahy, jak je znázorněno zde:

![Azure adresní prostor virtuální sítě s dvě mezery](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

V takovém případě **adresní prostor virtuální sítě** má dvě mezery, které jsou definovány. Tyto dvě mezery, jsou shodné s rozsahy IP adres, které jsou definovány pro **rozsah adres IP podsítě virtuálních počítačů Azure** a **rozsah adres IP podsíť brány virtuální sítě.**

Můžete použít všechny standardní pojmenování, který rádi používáte pro tyto podsítě tenanta (podsítí virtuálních počítačů). Ale **musí vždy obsahovat jeden a pouze jeden, podsíť brány pro každou virtuální síť** , která se připojuje k SAP HANA v okruhu ExpressRoute Azure (velké instance). A **tuto podsíť brány musí mít vždy název "GatewaySubnet"** zajistit správné umístění bránu ExpressRoute.

> [!WARNING] 
> Je velmi důležité, že podsíť brány vždy je s názvem "GatewaySubnet".

Více podsítí virtuálních počítačů může sloužit i využitím rozsahy adres nesouvislé. Ale jak už bylo zmíněno dříve, tyto rozsahy adres musí být pokryté komponentami **adresní prostor virtuální sítě** virtuální sítě v agregované formě nebo v seznamu přesné rozsahy adres podsítě virtuálních počítačů a podsíť brány.

Sumarizace důležitých faktů o virtuální síť Azure, která se připojuje k velké instance HANA:

- Budete potřebovat k odeslání společnosti Microsoft **adresní prostor virtuální sítě** při počátečním nasazení nástroje velkých instancích HANA. 
- **Adresní prostor virtuální sítě** může být jeden větší rozsah, které pokrývá rozsah pro rozsahy adres IP podsíť virtuálních počítačů Azure a rozsah adres IP podsíť brány virtuální sítě.
- Nebo můžete odeslat jako **adresní prostor virtuální sítě** rozsahy rozsahy adres IP podsíť virtuálních počítačů a rozsah adres IP podsíť brány virtuální sítě více oblastí, které se týkají různých IP adres.
- Určené **adresní prostor virtuální sítě** je použité šíření směrování protokolu BGP.
- Název podsítě brány musí být: **"GatewaySubnet".**
- **Adresní prostor virtuální sítě** slouží jako filtr na straně velká Instance HANA povolit nebo zakázat provoz s jednotkami velká Instance HANA v Azure. Pokud informace o směrování protokolu BGP z virtuální sítě Azure a rozsahy IP adres, které jsou nakonfigurované pro filtrování na straně velká Instance HANA neshodují, můžou nastat problémy v připojení.
- Existují některé podrobnosti o podsíti brány, které jsou popsané níže v části "propojení virtuální sítě ExpressRoute velké Instance HANA.



## <a name="different-ip-address-ranges-to-be-defined"></a>Chcete-li definovat různé rozsahy IP adres 

Představili už některé nezbytné k nasazení velkých instancích HANA v předchozích částech rozsahy IP adres. Existují některé další rozsahy IP adres, které jsou důležité. Podívejme se některé další podrobnosti. Následující IP adresy, které není nutné odeslat do Microsoftu, musíte je definovat, před odesláním požadavku pro počáteční nasazení:

- **Virtuální síť adresní prostor:** , jak už zavedené dříve, nebo jsou IP adresy rozsah(y) přiřadili jste (nebo plán přiřazení) pro vaše adresa místa parametr připojení virtuálních sítí (VNet) Azure do prostředí velkých instancí SAP HANA. Doporučuje se, že tento parametr adresní prostor je Víceřádkový hodnotu sestává z rozsahy podsíť virtuálních počítačů Azure a rozsahu podsítě brány Azure, jak je uvedeno v grafiky výše. Tento rozsah musí nepřekrývají s místním nebo fond IP adres serveru nebo ER P2P rozsahy adres. Tom, jak to nebo tyto rozsahy IP adres? Vaší podnikové síti týmu nebo poskytovali služeb, by měly poskytnout jednu nebo více IP adres rozsahy, které jsou nebo nejsou použity uvnitř vaší sítě. Příklad: Pokud vaše podsíť virtuálních počítačů Azure (viz výše) je 10.0.1.0/24 a podsítě brány Azure (viz dále) je 10.0.2.0/28, pak adresní prostor vaší virtuální síti Azure se doporučuje se dva řádky; 10.0.1.0/24 a 10.0.2.0/28. I když můžete agregovat hodnoty adresního prostoru, se doporučuje zjišťování shody s rozsahy adres podsítě zabránit náhodnému opakované použití nevyužité rozsahy IP adres v rámci větší adresních prostorů v budoucnu jinde ve vaší síti. **Adresní prostor virtuální sítě je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když s výzvou k počátečnímu nasazení**

- **Rozsah IP adres Azure VM podsítě:** tuto IP adresu rozsahu, jak je popsáno dříve již je ten, který jste přiřadili (nebo plán přiřazení) pro parametr virtuální sítě Azure podsíť ve virtuální síti Azure připojení k prostředí velkou instanci SAP HANA. Tento rozsah IP adres se používá k přiřazování IP adres pro virtuální počítače Azure. IP adresy mimo tento rozsah mohou připojit na vašich serverech velké Instance SAP HANA. V případě potřeby může sloužit několika podsítěmi virtuálních počítačů Azure. A/24 blok CIDR se společnost Microsoft doporučuje pro každou podsíť virtuálních počítačů Azure. Tento rozsah adres musí být součástí hodnot použitých v adresním prostoru virtuální sítě Azure. Jak získat tento rozsah IP adres? Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, které se aktuálně nepoužívá uvnitř vaší sítě.

- **Rozsah adres IP podsíť brány virtuální sítě:** závislosti na funkcích, které plánujete použít, doporučená velikost bude:
   - Bránu ExpressRoute úrovně Ultra-performance: / 26 blok adres - požadované pro SKU typu II třídu
   - Koexistence pomocí sítě VPN nebo ExpressRoute přes ExpressRoute Gateway High-performance (nebo menší): / 27 blok adres
   - Všechny ostatní případy: / 28 blok adres. Tento rozsah adres musí být součástí hodnot použitých v hodnotách "Adresní prostor virtuální sítě". Tento rozsah adres musí být součástí hodnot použitých v hodnoty adresní prostor virtuální sítě Azure, které budete potřebovat k odeslání společnosti Microsoft. Jak získat tento rozsah IP adres? Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, které se aktuálně nepoužívá uvnitř vaší sítě. 

- **Rozsah pro připojení k ER P2P adres:** tento rozsah je rozsah IP pro připojení k SAP HANA velké Instance ExpressRoute (ER) P2P. Tento rozsah IP adres musí být minimální velikostí/29 rozsah CIDR IP adres. Tento rozsah musí nepřekrývá s místním nebo dalších IP adres Azure rozsahy adres. Tento rozsah IP adres se používá k nastavení připojení ER ze brána ExpressRoute virtuální sítě Azure serverech velké Instance SAP HANA. Jak získat tento rozsah IP adres? Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, které se aktuálně nepoužívá uvnitř vaší sítě. **Tento rozsah je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když s výzvou k počátečnímu nasazení**
  
- **Rozsah adres fondu IP serveru:** jednotlivé IP adresu přiřadit k serverům velká instance HANA se používá tento rozsah IP adres. Velikost doporučená podsítě je/24 CIDR blokovat – ale pokud potřeby může být menší minimálně poskytnout 64 IP adresami. Z tohoto rozsahu prvních 30 IP adresy jsou vyhrazené pro použití společností Microsoft. Zkontrolujte, jestli že je tato skutečnost představoval při volbě velikosti oblasti. Tento rozsah musí nepřekrývá s místním nebo dalších IP adres Azure adresy. Jak získat tento rozsah IP adres? Vaší podnikové síti týmu nebo poskytovali služeb, by měla poskytnout rozsah IP adres, které se aktuálně nepoužívá uvnitř vaší sítě. / 24 (doporučeno) jedinečné CIDR blokovat má být použit pro přiřazení konkrétní IP adresy, které jsou potřebné pro SAP HANA v Azure (velké instance). **Tento rozsah je rozsah IP adres, které potřebuje k odeslání společnosti Microsoft, když s výzvou k počátečnímu nasazení**
 
I když je potřeba definovat a plánování rozsahů IP adres výše, ne všechny je třeba být předávány společnosti Microsoft. Slouží ke shrnutí výše, rozsahy IP adres, které jsou třeba název společnosti Microsoft jsou:

- Prostory adresu ve virtuální síti Azure
- Rozsah adres pro připojení k ER P2P
- Rozsah adres fondu IP serveru

Přidání dalších virtuálních sítí, která se potřebují připojit k velké instance HANA, vyžaduje, abyste k odeslání nový Azure adresní prostor virtuální sítě, které přidáváte do Microsoftu. 

Následuje příklad různé rozsahy a některé příklad rozsahy podle musíte nakonfigurovat a nakonec poskytovat společnosti Microsoft. Jak je vidět, hodnotu pro adresní prostor virtuální sítě Azure nejsou agregovány v prvním příkladu, ale je definována z rozsahů první IP adresa rozsahu podsítě virtuálních počítačů Azure a rozsah adres IP podsíť brány virtuální sítě. Použití více podsítí virtuálních počítačů v rámci virtuální sítě Azure by fungovat odpovídajícím způsobem podle konfigurace a odesílání dalších IP adres rozsahy další podsítě virtuálních počítačů v rámci adresního prostoru virtuální sítě Azure.

![Rozsahy IP adres v SAP HANA vyžaduje minimální nasazení Azure (velké instance)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Máte také možnost agregace dat, které odesíláte společnosti Microsoft. V takovém případě adresní prostor virtuální sítě Azure by obsahovat pouze jednu mezeru. Použití rozsahů IP adres použitých v příkladu výše. Může vypadat toto agregované virtuální síť adresní prostor:

![Druhá možnost vyžaduje v SAP HANA v Azure (velké instance) minimálního nasazení rozsahy IP adres](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak vidíte výše, místo dvě menší rozsahy definované adresní prostor virtuální sítě Azure, máme jeden větší rozsah, který zahrnuje 4096 IP adresy. Velké definice adresního prostoru znamená některé docela rozsáhlá rozsahy nevyužitá. Protože adresní prostor virtuální sítě hodnoty, které jsou použity pro šíření tras protokolu BGP, využití nevyužité oblasti v místním nebo jinde v síti mohou způsobit problémů se směrováním. Proto doporučujeme udržovat naprosto v souladu s adresním prostorem skutečné podsíť používá adresní prostor. V případě potřeby, aniž by došlo k výpadku ve virtuální síti, můžete vždy přidat nový adresní prostor hodnoty později.
 
> [!IMPORTANT] 
> Musí pro každou IP adresu rozsahu ER-P2P, IP fondu serverů, adresní prostor virtuálních sítí Azure **není** překrývat mezi sebou nebo někde jinde v síti používá jinou oblast; každý musí být diskrétní, a jako dvě grafiky nesmí být starší zobrazit, podsíť jinou oblast. Pokud dojde k překrytí mezi oblastmi, virtuální sítí Azure se nemůže připojit k okruhu ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Další kroky po definovali rozsahy adres
Po definování rozsahy IP adres je potřeba stát následující činnosti:

1. Rozsahy IP adres pro adresní prostor virtuální sítě Azure, ER P2P připojení a rozsah adres fondu IP serveru, můžete odešlete společně s další data, která byla uvedena na začátku dokumentu. V tomto okamžiku v okamžiku můžete také může začít vytvářet virtuální sítě a podsítě virtuálních počítačů. 
2. Okruh Express Route je vytvořen microsoftem mezi vašeho předplatného Azure a razítko velká Instance HANA.
3. Síť klienta se vytvoří na razítku velká Instance společností Microsoft.
4. Microsoft nakonfiguruje sítě v SAP HANA v Azure (velké instance) infrastrukturu tak, aby přijímal IP adresy z Azure VNet adresního prostoru, který komunikuje s velkých instancích HANA.
5. V závislosti na konkrétní SAP HANA v Azure (velké instance) SKU zakoupit Microsoft přiřadí výpočetní jednotka v síti klientů, přidělit a připojení úložiště a instalace operačního systému (SUSE nebo Red Hat Linux). IP adresy pro tyto jednotky se přesunete mimo fond IP adres serveru adresu rozsahu, které jste odeslali do Microsoftu.

Na konci procesu nasazení poskytuje Microsoft vám následující data:
- Informace potřebné pro připojení virtuálních sítích Azure pro okruh ExpressRoute, který se připojuje virtuální sítě Azure pro velké instance HANA:
     - Autorizace klíče
     - ExpressRoute PeerID
- Data pro přístup k velké instance HANA po vytvoření okruhu ExpressRoute a virtuální sítí VNet Azure.

Můžete také vyhledat posloupnost připojení velkých instancích HANA v dokumentu [komplexní nastavení pro velké instance SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Mnoho z následujících kroků jsou uvedeny v příkladu nasazení v tomto dokumentu. 

**Další kroky**

- Přečtěte si [propojení virtuální sítě ExpressRoute velká Instance HANA](hana-connect-vnet-express-route.md).