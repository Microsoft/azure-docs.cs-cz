---
title: Sítě důležité informace o integraci pro integrované systémy Azure Stack | Dokumentace Microsoftu
description: Zjistěte, co vám pomůžou naplánovat integrace sítě datového centra pomocí služby Azure Stack víc uzlů.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 48e2f85488adfb776da5f52c154028f8aafb167a
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617017"
---
# <a name="network-connectivity"></a>Připojení k síti
Tento článek obsahuje informace o síťové infrastruktury Azure Stack vám pomohou rozhodnout, jak nejlépe integrovat do vaší stávající síťové prostředí Azure Stack. 

> [!NOTE]
> Překládat externí názvy DNS z Azure Stack (například www.bing.com), budete muset zadat servery DNS pro předávání DNS požadavků. Další informace týkající se požadavků na Azure Stack DNS najdete v tématu [integrace datových center Azure Stack – DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Návrh fyzické sítě
Řešení Azure Stack vyžaduje odolné a vysoce dostupné fyzické infrastruktury pro podporu její provoz a služeb. Odchozí připojení ze systému ToR ohraničení přepínače jsou omezené na SFP + nebo SFP28 média a 1 GB, 10 GB nebo rychlosti 25 GB. Obraťte se na dodavatele hardwaru výrobce OEM (OEM), dostupnost. V níže uvedeném diagramu náš doporučený návrh:

![Doporučený návrh sítě Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logické sítě
Logické sítě představují abstrakci podkladové fyzické síťové infrastruktury. Slouží k organizaci a zjednodušení přiřazení v síti pro hostitele, virtuální počítače a služby. Jako součást vytváření logické sítě síťové lokality nejsou vytvářeny definovat virtuální místní sítě (VLAN), podsítě IP a páry podsíť VLAN IP, které jsou spojené s logickou sítí v každém fyzickém umístění.

V následující tabulce jsou uvedeny logické sítě a přidružené podsítě rozsahy IPv4, které je nutné naplánovat:

| Logické sítě | Popis | Velikost | 
| -------- | ------------- | ------------ | 
| Veřejné virtuální IP adresy | Azure Stack používá celkem 31 adres z této sítě. Osm veřejné IP adresy se používají pro malou skupinu služby Azure Stack a zbývající jsou používány tenantské virtuální počítače. Pokud máte v plánu služby App Service a poskytovatele prostředků SQL, 7 další adresy se používají. Zbývající 15 IP adresy jsou vyhrazené pro budoucí služby Azure. | / 26 (62 hostitelů) - /22 (1022 hostitelů)<br><br>Doporučené = /24 (254 hostiteli) | 
| Přepínač infrastruktury | Point-to-Point IP adresy pro účely směrování, vyhrazené přepnou rozhraní pro správu a zpětné smyčky adresy přiřazené k přepínači. | /26 | 
| Infrastruktura | Ke komunikaci se používá pro interní komponenty služby Azure Stack. | /24 |
| Private | Používá pro síť úložiště a privátní virtuální IP adresy. | /24 | 
| BMC | Slouží ke komunikaci s pro správu základní desky na fyzických hostitelích. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Síťové infrastruktury
Síťové infrastruktury pro Azure Stack se skládá z několika logických sítí, které jsou nakonfigurované v přepínačích. Následující diagram znázorňuje tyto logické sítě a jak integrovat top-of-rack (TOR), řadiče pro správu základní desky (BMC) a ohraničení přepínače (síť zákazníka).

![Diagram a přepínač připojení logické sítě](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Síť BMC
Tuto síť je vyhrazen pro všechny řadiče pro správu základní (označované také jako služba procesory, například iDRAC Lights-out, iBMC, atd.) připojení k síti pro správu. Pokud jsou k dispozici, Hardware životního cyklu hostitelů (HLH) se nachází v této síti a může poskytnout specifické pro výrobce OEM softwaru pro hardware údržbou nebo monitorováním. 

HLH také hostitelem nasazení virtuálního počítače (DVM). DVM se používá při nasazení Azure Stack a odebrání po dokončení nasazení. DVM vyžaduje přístup k Internetu ve scénářích nasazení připojené k testování, ověřování a přístup k více komponent. Tyto komponenty mohou být uvnitř i vně vaší podnikové sítě. například NTP, DNS a Azure. Další informace o požadavcích na připojení, najdete v článku [části NAT v integrace brány firewall služby Azure Stack](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Privátní síť
Tato /24 (254 hostiteli IP adresy) síť patří do oblasti Azure Stack (Nerozbaluje nad rámec zařízení přepínač ohraničení oblasti Azure Stack) a je rozdělen do dvou podsítí:

- **Síť úložiště**. /25 (126 hostitele IP adresy) síť používaná pro podporu použití prostorů s přímým přístupem a zprávy bloku SMB (Server) přenosy dat úložiště a migrace za provozu virtuálního počítače. 
- **Interní virtuální síti IP**. A/25 síť vyhrazený pro interní čistě virtuální IP adresy nástroje pro vyrovnávání zatížení softwaru.

### <a name="azure-stack-infrastructure-network"></a>Síť infrastruktury Azure stacku
To/24 sítě je vyhrazený pro interní komponenty služby Azure Stack, mohli komunikovat a vyměňovat data mezi sebou. Tato podsíť vyžaduje směrovatelné IP adresy, ale se ukládají privátní do řešení pomocí seznamů řízení přístupu (ACL). Neočekává se bude směrovat nad rámec ohraničení přepínače s výjimkou ekvivalentní velikost/27 malé rozsah sítě využít některé z těchto služeb v případě, které vyžadují přístup k externím prostředkům a/nebo Internetu. 

### <a name="public-infrastructure-network"></a>Infrastruktura veřejných sítě
To/27. síť je malé rozsah z podsítě infrastruktury Azure stacku, již bylo zmíněno dříve, nevyžaduje veřejné IP adresy, ale vyžaduje přístup k Internetu prostřednictvím překladu adres nebo transparentní proxy server. Tato síť bude přidělena pro nouzovou obnovení konzoly systému (ERCS), virtuální počítač ERCS vyžaduje přístup k Internetu během registrace do Azure a během zálohování infrastruktury. Virtuální počítač ERCS musí být směrovatelné na vaši síť pro správu pro účely odstraňování potíží.

### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres
Síť veřejných virtuálních IP adres se přiřadí k síťovému adaptéru ve službě Azure Stack. Nejedná se o logickou síť, na přepínač. Nástroj SLB pomocí fond adres a přiřadí/32 sítě pro úlohy klientů. Ve směrovací tabulce přepínače jsou tyto 32 IP adresy inzerované jako k dispozici směrování přes BGP. Tato síť obsahuje externí přístupné nebo veřejné IP adresy. Infrastruktura Azure stacku si vyhrazuje prvních 31 adresy z této veřejné sítě VIP, zatímco zbytek používají klientské virtuální počítače. Velikost sítě v této podsíti můžete být v rozsahu v rozmezí od /26 (64 hostitelů) na maximálně /22 (1022 hostitelů), doporučujeme, abyste při plánování/24 je síť.

### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury
To/26 síť je podsíť, která obsahuje podsítě směrovatelné point-to-point/IP 30 (2 hostitele IP adresy) a vytváření zpětných smyček, které jsou vyhrazené/32 podsítě pro integrované přepnout správy a ID směrovače protokolu BGP Tento rozsah IP adres musí být směrovatelné externě ze služby Azure Stack řešení do vašeho datového centra, mohou být privátní nebo veřejné IP adresy.

### <a name="switch-management-network"></a>Síť pro správu přepínače
Tato minimální velikostí/29 (6 hostitele IP adresy) síť je vyhrazená pro připojování porty přepínače pro správu. To umožňuje přístup out-of-band pro nasazení, správu a řešení potíží. Počítá se od sítě infrastruktury přepínač uvedených výše.

## <a name="publish-azure-stack-services"></a>Publikování služby Azure Stack
Bude potřeba zpřístupnit služby Azure Stack uživatelů z mimo Azure Stack. Azure Stack nastaví různé koncové body pro jeho infrastrukturu role. Tyto koncové body se přiřadí virtuální IP adresy z fondu veřejných IP adres. Položka DNS se vytvoří pro každý koncový bod v externí zónu DNS, který byl zadán v době nasazení. Portál user portal je přiřadit například záznam hostitele DNS portálu.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*.

### <a name="ports-and-urls"></a>Porty a adresy URL
Do služby Azure Stack (například na portálech Azure Resource Manageru, DNS, atd.) dostupná pro externí sítě, musíte povolit příchozí přenosy s těmito koncovými body pro konkrétní adresy URL, porty a protokoly.
 
V nasazení tam, kde transparentní proxy server odchozí připojení k tradiční proxy server, musíte povolit určité porty a adresy URL pro obě [příchozí](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) a [odchozí](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) komunikace. Patří mezi ně portů a adres URL pro identity, na marketplace, opravy a aktualizace, registraci a data o využití.

## <a name="next-steps"></a>Další postup
[Připojení k ohraničení](azure-stack-border-connectivity.md)