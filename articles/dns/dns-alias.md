---
title: Azure DNS alias Přehled záznamů
description: Přehled podpory pro záznamů aliasů v Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52653252df3efd3e12fa974ed82cd2557eee93d0
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301243"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias Přehled záznamů

Azure DNS záznamů aliasů jsou požadavky na sady záznamů DNS. Mohou odkazovat další prostředky Azure z v rámci zóny DNS. Můžete například vytvořit skupinu záznam aliasu, který odkazuje veřejnou IP adresou Azure místo záznam. Alias záznamu sady bodů Azure veřejnou IP adresu instance služby dynamicky. V důsledku toho sadu záznamů alias bezproblémově se aktualizuje sám během překlad DNS.

Sady záznamů alias je podporována pro následující typy záznamů v zóně Azure DNS: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Alias záznamy A nebo AAAA typy záznamů pro službu Azure Traffic Manager jsou podporovány pouze pro typy externí koncový bod. Je nutné zadat adresu IPv4 nebo IPv6, případně pro externí koncové body Traffic Manageru. V ideálním případě by použijte statické IP adresy pro adresu.

## <a name="capabilities"></a>Možnosti

- **Odkazovat na prostředek veřejné IP adresy ze serveru DNS A/AAAA sadu záznamů.** Můžete vytvořit sadu záznamů A/AAAA a nastavte ji alias záznamu sady tak, aby odkazoval na prostředek veřejné IP adresy.

- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME.** Ze sady záznamů DNS CNAME může odkazovat na CNAME profil služby Traffic Manager. Příkladem je contoso.trafficmanager.net. Teď budete také může odkazovat na profil Traffic Manageru, který má externí koncové body z záznam A nebo AAAA, nastavte ve vaší zóně DNS.

   > [!NOTE]
   > Alias záznamy A nebo AAAA typy záznamů pro Traffic Manager jsou podporovány pouze pro typy externí koncový bod. Je nutné zadat adresu IPv4 nebo IPv6, případně pro externí koncové body Traffic Manageru. V ideálním případě by použijte statické IP adresy pro adresu.
   
- **Přejděte na jinou sadu záznamů DNS v rámci stejné zóny.** Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Sady záznamů DNS CNAME může být například alias pro jinou sadu záznamů CNAME stejného typu. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů bude aliasy a některé jiné aliasy.

## <a name="scenarios"></a>Scénáře
Existuje několik běžných scénářů pro záznamů aliasů.

### <a name="prevent-dangling-dns-records"></a>Zabránit nepropojená záznamů DNS
 V rámci zóny Azure DNS záznamů aliasů umožňuje úzce udržovat přehled o životním cyklu prostředků Azure. Prostředky zahrnují veřejnou IP adresu nebo profil služby Traffic Manager. Běžný problém u tradiční záznamů DNS je nepropojená záznamy. Tomuto problému dochází, zejména s A/AAAA, CNAME nebo typy záznamů. 

Tradiční zaznamenané zóny DNS Pokud cílová IP adresa nebo CNAME už existuje, záznam DNS zóny neví ho. V důsledku toho záznam se musí aktualizovat ručně. V některých organizacích nemusí dojít Tento ruční aktualizace v čase. Také může být problematický z důvodu oddělení rolí a úrovní oprávnění přidružené.

Například role může mít oprávnění k odstranění CNAME nebo IP adresu, které patří k aplikaci. Ale nemá dostatečná oprávnění k aktualizaci záznamu DNS, který odkazuje na tyto cíle. Doba zpoždění nastane mezi IP adresu nebo CNAME se odstraní a odebere se záznam DNS, který odkazuje na ni. Tuto časovou prodlevu potenciálně způsobit, že kvůli výpadku pro uživatele.

Záznamů aliasů zbavte se složité přidružené k této situaci. Mohou pomoci zabránit nepropojená odkazy. Vezměme si jako příklad záznamu DNS, který je kvalifikován jako záznamu o aliasu tak, aby odkazoval na veřejnou IP adresu nebo profil služby Traffic Manager. Pokud těchto základních prostředků se odstraní, odebere se záznam aliasu DNS ve stejnou dobu. Tento proces zajišťuje, že uživatelé nikdy dochází kvůli výpadku.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Aktualizovat DNS zóny automaticky při změně IP adresy aplikace

Tento scénář je podobný předchozímu. Možná se přesune aplikace nebo základní virtuální počítač se restartuje. Záznam aliasu pak se automaticky aktualizuje při změně IP adresu pro základní veřejný IP prostředek. Tím se vyhnete potenciální bezpečnostní rizika směruje uživatele do jiné aplikace, která obsahuje starou IP adresu.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostování aplikací s vyrovnáváním zatížení ve vrcholu zóny

Protokol DNS brání přiřazení čehokoli jiného, než záznam A nebo AAAA ve vrcholu zóny. Příklad: contoso.com. Toto omezení představuje problém pro počet vlastníků aplikace, kteří mají aplikace s vyrovnáváním zatížení za Traffic Manager. Není možné tak, aby odkazoval na profil Traffic Manageru z vrcholu zóny. Počet vlastníků aplikace v důsledku toho musíte použít alternativní řešení. Přesměrování na aplikační vrstvě musí přesměrovat adresu z vrcholu zóny na jinou doménu. Je například přesměrování z contoso.com na www.contoso.com. Toto uspořádání představuje jediný bod selhání pro funkci přesměrování.

Pomocí záznamů aliasů tento problém již neexistuje. Počet vlastníků aplikace teď může odkazovat jejich záznamu vrcholu zóny na profil Traffic Manageru, který má externí koncové body. Počet vlastníků aplikace může odkazovat na stejný profil Traffic Manageru, který se používá u všech ostatních domén v rámci jejich zóny DNS. Například contoso.com a www.contoso.com může odkazovat na stejný profil Traffic Manageru. To platí za předpokladu, profil služby Traffic Manager má jenom externí nakonfigurované koncové body.

## <a name="next-steps"></a>Další postup

Další informace o záznamů aliasů, naleznete v následujících článcích:

- [Kurz: Konfigurace záznamu o aliasu odkazovat na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
