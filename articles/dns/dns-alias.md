---
title: Přehled záznamů aliasů – Azure DNS
description: V tomto článku se dozvíte o podpoře záznamů aliasů v Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295492"
---
# <a name="azure-dns-alias-records-overview"></a>Přehled záznamů aliasů Azure DNS

Záznamy aliasů Azure DNS jsou kvalifikace v sadě záznamů DNS. Můžou odkazovat na jiné prostředky Azure z vaší zóny DNS. Můžete například vytvořit sadu záznamů aliasu, která odkazuje na veřejnou IP adresu Azure namísto záznamu A. Sada záznamů aliasu se dynamicky řídí instancí služby veřejné IP adresy Azure. V důsledku toho se sada záznamů aliasu bezproblémově aktualizuje během překladu DNS.

Sada záznamů aliasu je podporovaná pro následující typy záznamů v zóně Azure DNS: 

- A
- AAAA
- CNAME

> [!NOTE]
> Pokud máte v úmyslu použít záznam aliasu pro typy záznamů A nebo AAAA k přejděte na [profil Azure Traffic Manager,](../traffic-manager/quickstart-create-traffic-manager-profile.md) musíte se ujistit, že profil Traffic Manager unás má pouze [externí koncové body](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Je nutné zadat adresu IPv4 nebo IPv6 pro externí koncové body ve Správci provozu. V koncových bodech nelze používat plně kvalifikované názvy domén (FQDNs). V ideálním případě použijte statické IP adresy.

## <a name="capabilities"></a>Možnosti

- **Přejděte na veřejný prostředek IP ze sady záznamů DNS A/AAAA.** Můžete vytvořit sadu záznamů A/AAAA a nastavit její záznam aliasu nastavený tak, aby ukazoval na veřejný prostředek IP (standardní nebo základní). Nastavení záznamu DNS se automaticky změní, pokud se veřejná IP adresa změní nebo bude odstraněna. Vyhnutí se záznamům DNS, které odkazují na nesprávné adresy IP, se vyhnete.

   Existuje aktuální limit 20 sad záznamů aliasů na prostředek.

- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME.** Můžete vytvořit sadu záznamů A/AAAA nebo CNAME a použít záznamy aliasů, které nasměrují na profil Traffic Manageru. To je užitečné zejména v případě, že potřebujete směrovat provoz na vrcholu zóny, protože tradiční záznamy CNAME nejsou pro vrchol zóny podporovány. Řekněme například, že profil Traffic Manageru je myprofile.trafficmanager.net a že vaše obchodní zóna DNS je contoso.com. Můžete vytvořit sadu záznamů aliasu typu A/AAAA pro contoso.com (vrchol zóny) a přejděte na myprofile.trafficmanager.net.
- **Přejděte na koncový bod sítě doručování obsahu Azure (CDN).** To je užitečné při vytváření statických webů pomocí azure storage a Azure CDN.
- **Přejděte na jiný záznam DNS nastavený ve stejné zóně.** Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Například sada záznamů DNS CNAME může být alias pro jinou sadu záznamů CNAME. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů byly aliasy a některé nealiasy.

## <a name="scenarios"></a>Scénáře

Existuje několik běžných scénářů pro záznamy Alias.

### <a name="prevent-dangling-dns-records"></a>Zabránit visícím záznamům DNS

Běžným problémem s tradičními záznamy DNS jsou visící záznamy. Například záznamy DNS, které nebyly aktualizovány tak, aby odrážely změny adres IP. K problému dochází zejména s Typy záznamů A/AAAA nebo CNAME.

Pokud cílová adresa IP nebo CNAME již neexistuje, musí být s tradičním záznamem zóny DNS ručně aktualizována. V některých organizacích nemusí dojít k ruční aktualizaci včas z důvodu problémů s procesem nebo oddělení rolí a přidružených úrovní oprávnění. Role může mít například oprávnění k odstranění adresy CNAME nebo IP, která patří do aplikace. Ale nemá dostatečnou autoritu k aktualizaci záznamu DNS, který odkazuje na tyto cíle. Zpoždění při aktualizaci záznamu DNS může potenciálně způsobit výpadek pro uživatele.

Alias záznamy zabránit visící odkazy tím, že pevně propojení životního cyklu záznamu DNS s prostředkem Azure. Zvažte například záznam DNS, který je kvalifikován jako záznam aliasu, který přejde na veřejnou IP adresu nebo profil Traffic Manageru. Pokud odstraníte tyto podkladové prostředky, stane se záznam aliasu DNS prázdnou sadou záznamů. Již odkazuje na odstraněný prostředek.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Automatická aktualizace záznamu DNS při změně ip adres aplikace

Tento scénář je podobný předchozímu scénáři. Možná je aplikace přesunuta nebo je restartován základní virtuální počítač. Záznam aliasu se pak automaticky aktualizuje, když se změní adresa IP pro základní veřejný prostředek IP. Tím se zabrání potenciálním bezpečnostním rizikům přesměrování uživatelů na jinou aplikaci, které byla přiřazena stará veřejná IP adresa.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostitelské aplikace s vyrovnáváním zatížení na vrcholu zóny

Protokol DNS zabraňuje přiřazení záznamů CNAME na vrcholu zóny. Například pokud je vaše doména contoso.com; můžete vytvořit záznamy CNAME pro somelabel.contoso.com; ale nemůžete vytvořit CNAME pro contoso.com sám.
Toto omezení představuje problém pro vlastníky aplikací, kteří mají aplikace s vyrovnáváním zatížení za [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vzhledem k tomu, že použití profilu Traffic Manageru vyžaduje vytvoření záznamu CNAME, není možné ukázat na profil Traffic Manager z vrcholu zóny.

Tento problém je vyřešen pomocí alias záznamů. Na rozdíl od záznamů CNAME jsou záznamy aliasů vytvářeny v vrcholu zóny a vlastníci aplikací je mohou použít k navedení záznamu vrcholu zóny na profil Traffic Manageru, který má externí koncové body. Vlastníci aplikací odkazují na stejný profil Traffic Manageru, který se používá pro jakoukoli jinou doménu v rámci jejich zóny DNS.

Například contoso.com a\.www contoso.com mohou překážet na stejný profil traffic manageru. Další informace o používání záznamů aliasů v profilech Azure Traffic Manageru najdete v části Další kroky.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Vrchol bodové zóny ke koncovým bodům Azure CDN

Stejně jako profil Traffic Manageru můžete také použít záznamy aliasu k namičování vrcholu zóny DNS na koncové body Azure CDN. To je užitečné při vytváření statických webů pomocí azure storage a Azure CDN. Poté můžete přistupovat na webové stránky bez nutnosti "www" na váš název DNS.

Například pokud statické webové `www.contoso.com, your users can access your site using contoso.com` stránky je pojmenován bez nutnosti předřadit www na název DNS.

Jak je popsáno výše, CNAME záznamy nejsou podporovány na vrcholu zóny. Takže nelze použít záznam CNAME k překážení contoso.com koncového bodu CDN. Místo toho můžete použít záznam aliasu k přímému namičit vrchol zóny na koncový bod CDN.

> [!NOTE]
> Směrování vrcholu zóny na koncové body CDN pro Azure CDN z Akamai není momentálně podporováno.

## <a name="next-steps"></a>Další kroky

Další informace o záznamech aliasů najdete v následujících článcích:

- [Kurz: Konfigurace záznamu aliasu tak, aby odkazoval na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
