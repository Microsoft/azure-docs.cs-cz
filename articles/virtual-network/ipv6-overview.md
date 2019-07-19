---
title: Přehled protokolu IPv6 pro Azure Virtual Network (Preview)
titlesuffix: Azure Virtual Network
description: Popis IPv6 koncových bodů IPv6 a cest k datům ve službě Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249836"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co je protokol IPv6 pro Azure Virtual Network? (Preview)

Protokol IPv6 pro Azure Virtual Network (VNet) umožňuje hostovat aplikace v Azure s připojením IPv6 a IPv4 jak v rámci virtuální sítě, tak i z Internetu. V důsledku vyčerpání veřejných IPv4 adres jsou nové sítě pro mobilitu a Internet věcí (IoT) často založené na protokolu IPv6. Dokonce i dlouhodobě zavedené sítě poskytovatele internetových služeb a mobilních sítí jsou transformované na IPv6. Služby jenom s protokolem IPv4 můžou na stávajících i nově vznikajících trzích najít skutečnou nevýhody. Připojení IPv4/IPv6 s duálním zásobníkem umožňuje službám hostovaným v Azure procházet tuto softwarovou mezeru pomocí globálně dostupných, duálních služeb, které se dají snadno připojit k existujícím IPv4 i těmto novým zařízením a sítím s protokolem IPv6.

Původní připojení IPv6 v Azure usnadňuje poskytování připojení k internetu přes duální zásobník (IPv4/IPv6) pro aplikace hostované v Azure. Umožňuje jednoduché nasazení virtuálních počítačů s připojením IPv6 s vyrovnáváním zatížení pro příchozí i odchozí připojení iniciovaná. Tato funkce je stále k dispozici a další informace jsou k dispozici [zde](../load-balancer/load-balancer-ipv6-overview.md).
Protokol IPv6 pro službu Azure Virtual Network je mnohem více úplný a umožňuje nasazení úplných architektur řešení IPv6 v Azure.

> [!Important]
> Protokol IPv6 pro Azure Virtual Network je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Následující diagram znázorňuje jednoduché nasazení duálního zásobníku (IPv4/IPv6) v Azure:

![Diagram nasazení sítě IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Výhody

Výhody Azure Virtual Network IPv6:

- Pomáhá rozšířit dosah vašich aplikací hostovaných v Azure na rostoucí mobilní a Internet věcí trhy.
- Duální skládané virtuální počítače IPv4/IPv6 poskytují maximální flexibilitu nasazení služeb. Jedna instance služby se může připojit k internetovým klientům podporujícím IPv4 i IPv6.
- Vychází z dlouhotrvajícího a stabilního připojení k protokolu IPv6 mezi virtuálními počítači v síti Azure.
- Zabezpečení ve výchozím nastavení, protože připojení IPv6 k Internetu je navázáno pouze v případě, že je výslovně vyžádáte ve svém nasazení.

## <a name="capabilities"></a>Možnosti

Protokol IPv6 pro virtuální síť obsahuje následující funkce:

- Zákazníci Azure můžou definovat vlastní adresní prostor virtuální sítě IPv6, který bude vyhovovat potřebám svých aplikací, zákazníkům nebo bezproblémové integraci do svého místního IP prostoru.
- Virtuální sítě duálního zásobníku (IPv4 a IPv6) s podsítěmi s duálním zásobníkem umožňují aplikacím připojení pomocí prostředků IPv4 i IPv6 ve své virtuální síti nebo v Internetu.
- Chraňte své prostředky pomocí pravidel IPv6 pro skupiny zabezpečení sítě.
- Přizpůsobte směrování provozu protokolu IPv6 ve virtuální síti pomocí uživatelem definovaných tras – zejména při využití síťových virtuálních zařízení k rozšíření vaší aplikace.
- Umožněte internetovým klientům bezproblémový přístup k aplikaci s duálním zásobníkem pomocí vlastního protokolu, který je Azure DNS podpora záznamů IPv6 (AAAA). 
- Standardní podpora IPv6 veřejných nástrojů pro vyrovnávání zatížení k vytváření odolných a škálovatelných aplikací, která zahrnuje:
    - Volitelná sonda stavu protokolu IPv6, která určuje, které instance fondu back-endu jsou v pořádku, a tedy může přijímat nové toky. .  
    - Volitelná odchozí pravidla, která poskytují úplnou deklarativní kontrolu nad odchozím připojením ke škálování a optimalizaci této možnosti podle vašich konkrétních potřeb.
    - Volitelné vícenásobné konfigurace front-endu, které umožňují, aby jeden nástroj pro vyrovnávání zatížení používal více veřejných IP adres IPv6 – stejný protokol a port front-end je možné znovu použít napříč adresami front-endu.
- Veřejná IP adresa na úrovni instance poskytuje připojení k Internetu IPv6 přímo k jednotlivým virtuálním počítačům.
- Můžete snadno přidat připojení IPv6 k existujícím nasazením jenom s protokolem IPv4 s upgradem na místě.
- Vytvářejte aplikace s duálním zásobníkem, které se automaticky škálují na vaše zatížení pomocí služby Virtual Machine Scale Sets.
- Podpora portálu pro verzi Preview zahrnuje interaktivní vytváření, úpravy a odstraňování virtuálních sítí a podsítí pro duální zásobníky (IPv4 + IPv6), pravidla skupiny zabezpečení sítě IPv6, trasy definované uživatelem protokolu IPv6 a veřejné IP adresy IPv6.  

## <a name="limitations"></a>Omezení
Verze Preview protokolu IPv6 pro službu Azure Virtual Network má tato omezení:
- Protokol IPv6 pro Azure Virtual Network (Preview) je k dispozici ve všech globálních oblastech Azure, ale jenom v globálním cloudu Azure, nikoli v cloudech státní správy.
- Podpora portálu pro Standard Load Balancer komponenty je jen pro čtení.  Plná podpora a dokumentace (s ukázkami) je ale k dispozici pro Standard Load Balancer nasazení pomocí Azure PowerShellu a rozhraní příkazového řádku (CLI).   
- Podpora Network Watcher pro verzi Preview je omezená na protokoly toku NSG a Zachytávání síťových paketů.
- Partnerský vztah virtuální sítě (regionální nebo globálně) není ve verzi Preview podporován.
- Při použití standardního externího Load Balancer IPv6 platí následující omezení: 
  - Odchozí pravidla můžou odkazovat na několik veřejných IP adres front-endu, ale **nemusí odkazovat na** veřejnou předponu IPv6. Veřejná Předpona protokolu IP podporuje pouze předpony protokolu IPv4.
  - Pravidla vyrovnávání zatížení IPv6 **nesmí používat** funkci *plovoucí IP adresy* . Opětovné použití portu na back-end instancí se podporuje jenom s protokolem IPv4.
- Funkce předpony veřejných IP adres Azure nepodporuje rezervaci bloku internetových adres IPv6.

## <a name="pricing"></a>Ceny

Prostředky a šířka pásma Azure Azure se účtují stejnou sazbou jako IPv4. Pro protokol IPv6 se neúčtují žádné další ani jiné poplatky. Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [nasadit aplikaci s duálním zásobníkem IPv6 pomocí Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Naučte se, jak [pomocí Azure CLI nasadit aplikaci s duálním zásobníkem IPv6](virtual-network-ipv4-ipv6-dual-stack-cli.md).
