---
title: Nejčastější dotazy – Azure DNS
description: V tomto článku se dozvíte o nejčastějších dotazech k Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246378"
---
# <a name="azure-dns-faq"></a>Nejčastější dotazy k Azure DNS

## <a name="about-azure-dns"></a>O Azure DNS

### <a name="what-is-azure-dns"></a>Co je Azure DNS?

DNS (Domain Name System) překládá nebo řeší název webu nebo služby na jeho IP adresu. Azure DNS je hostitelská služba pro domény DNS. Zajišťuje překlad názvů pomocí Microsoft Azure infrastruktury. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Domény DNS v Azure DNS hostují globální síť názvových serverů DNS v Azure. Tento systém využívá síť s libovolným všesměrovým vysíláním, aby každý dotaz DNS byl zodpovězen nejbližším dostupným serverem DNS. Azure DNS poskytuje pro vaši doménu rychlý výkon a vysokou dostupnost.

Azure DNS je založen na Azure Resource Manager. Azure DNS výhody z funkcí Správce prostředků, jako je řízení přístupu na základě role Azure, protokoly auditu a uzamykání prostředků. Domény a záznamy můžete spravovat prostřednictvím Azure Portal, Azure PowerShell rutin a Azure CLI pro různé platformy. Aplikace, které vyžadují automatickou správu DNS, se dají integrovat se službou prostřednictvím REST API a sad SDK.

### <a name="how-much-does-azure-dns-cost"></a>Kolik se Azure DNS náklady?

Model fakturace Azure DNS vychází z počtu zón DNS hostovaných v Azure DNS. Vychází taky z počtu dotazů DNS, které obdrží. Slevy se poskytují na základě využití.

Další informace najdete na stránce s [cenami Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Co je smlouva SLA pro Azure DNS?

Azure zaručuje, že platné požadavky DNS obdrží odpověď aspoň z jednoho Azure DNS názvového serveru 100% času.

Další informace najdete na stránce věnované [smlouvě SLA Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co je zóna DNS? Je stejný jako doména DNS? 

Doména je jedinečný název v systému DNS (Domain Name System). Příklad: contoso.com.

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Například doména contoso.com může obsahovat několik záznamů DNS. Tyto záznamy můžou zahrnovat mail.contoso.com pro poštovní server a webové \. contoso.com pro web. Tyto záznamy jsou hostované v zóně DNS contoso.com.

Název domény je *jenom název*. Zóna DNS je datový prostředek, který obsahuje záznamy DNS pro název domény. Azure DNS můžete použít k hostování zóny DNS a správě záznamů DNS pro doménu v Azure. Poskytuje taky názvové servery DNS pro zodpovězení dotazů DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Potřebuji si koupit název domény DNS, který se má použít Azure DNS? 

Ne nutně.

Nemusíte kupovat doménu pro hostování zóny DNS v Azure DNS. Zónu DNS můžete kdykoli vytvořit bez vlastnictví názvu domény. Dotazy DNS pro tuto zónu se vyřeší pouze v případě, že jsou směrovány na Azure DNS názvové servery přiřazené k zóně.

Pokud chcete zónu DNS propojit s globální hierarchií DNS, musíte si koupit název domény. Pak dotazy DNS z libovolného místa na světě hledají vaši zónu DNS a odpověď se záznamy DNS.

## <a name="azure-dns-features"></a>Azure DNS funkce

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existují nějaká omezení při použití záznamů aliasů pro název domény ve vrcholu s Traffic Manager?

Ano. Pro Azure Traffic Manager je nutné použít statické veřejné IP adresy. Nakonfigurujte cíl **externího koncového bodu** pomocí statické IP adresy. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Podporuje Azure DNS směrování provozu založeného na DNS nebo převzetí služeb při selhání koncového bodu?

Směrování provozu na základě DNS a převzetí služeb při selhání koncových bodů poskytuje Traffic Manager. Traffic Manager je samostatná služba Azure, kterou lze použít s Azure DNS. Další informace najdete v [přehledu Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS podporuje pouze hostování statických domén DNS, kde každý dotaz DNS pro daný záznam DNS vždy obdrží stejnou odpověď DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Podporuje Azure DNS registraci názvů domén?

No. Azure DNS v současné době nepodporuje možnost nákupu názvů domén. K nákupu domén je nutné použít registrátora názvu domény třetí strany. Registrátor obvykle účtuje malý roční poplatek. Domény pak můžou být hostované v Azure DNS pro správu záznamů DNS. Další informace najdete v tématu [delegování domény na Azure DNS](dns-domain-delegation.md).

Funkce pro nákup názvů domén se sleduje v nevyřízených položkách Azure. K [registraci podpory pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)použijte web pro zpětnou vazbu.

### <a name="does-azure-dns-support-dnssec"></a>Podporuje Azure DNS DNSSEC?

No. Azure DNS v současné době nepodporuje rozšíření DNSSEC (Domain Name System Security Extensions).

Funkce DNSSEC je sledována v Azure DNS nevyřízené položky. K [registraci podpory pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)použijte web pro zpětnou vazbu.

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Podporuje Azure DNS přenos zón (AXFR/IXFR)?

No. Azure DNS v současné době nepodporuje přenosy zón. Zóny DNS se dají [do Azure DNS importovat pomocí Azure CLI](dns-import-export.md). Záznamy DNS se spravují prostřednictvím [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [REST API](/powershell/module/az.dns), [sady SDK](dns-sdk.md), [rutin PowerShellu](dns-operations-recordsets.md)nebo [nástroje CLI](dns-operations-recordsets-cli.md).

Funkce přenosu zóny je sledována v Azure DNS nevyřízené položky. K [registraci podpory pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)použijte web pro zpětnou vazbu.

### <a name="does-azure-dns-support-url-redirects"></a>Podporuje Azure DNS přesměrování adresy URL?

No. Služby přesměrování adresy URL nejsou službou DNS. Fungují na úrovni HTTP, nikoli na úrovni DNS. Někteří poskytovatelé DNS naváže službu přesměrování adresy URL jako součást své celkové nabídky. Služba Azure DNS aktuálně nepodporuje tuto službu.

Funkce přesměrování adresy URL je sledována v Azure DNS nevyřízené položky. K [registraci podpory pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)použijte web pro zpětnou vazbu.

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Podporuje Azure DNS rozšířené kódování ASCII (8bitové) nastavené pro sady záznamů TXT?

Ano. Azure DNS podporuje sadu rozšířených kódování ASCII pro sady záznamů TXT. Musíte ale použít nejnovější verzi rozhraní Azure REST API, sady SDK, PowerShellu a rozhraní příkazového řádku. Verze starší než 1. října 2017 nebo SDK 2,1 nepodporují rozšířené sady ASCII. 

Jako hodnotu pro záznam TXT můžete zadat například řetězec, který má rozšířený znak ASCII \ 128. Příkladem je "abcd\128efgh." Azure DNS používá hodnotu bajtu tohoto znaku, který je 128 v interní reprezentaci. V době překladu DNS se tato bajtová hodnota vrátí v odpovědi. Všimněte si také, že "ABC" a "\ 097 \ 098 \ 099" jsou zaměnitelné, pokud jde o řešení. 

Pro záznamy TXT dodržujeme pravidla řídicího vzoru souboru zóny [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) . Například `\` nyní ve skutečnosti řídí vše na základě RFC. Pokud zadáte `A\B` jako hodnotu záznamu TXT, bude reprezentována a vyřešena stejně `AB` . Pokud opravdu chcete, aby měl záznam TXT `A\B` na rozlišení, je nutné znovu spustit řídicí znak `\` . Jako příklad zadejte `A\\B` .

Tato podpora není aktuálně k dispozici pro záznamy TXT vytvořené z Azure Portal.

## <a name="alias-records"></a>Záznamy aliasů

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Jaké jsou některé scénáře, kde jsou záznamy aliasů užitečné?

Další informace najdete v části scénáře v tématu [Azure DNS – přehled záznamů aliasů](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jaké typy záznamů jsou podporovány pro sady záznamů aliasů?

Sady záznamů aliasů jsou podporovány pro následující typy záznamů v zóně Azure DNS:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jaké prostředky se podporují jako cíle pro sady záznamů aliasů?

- **Najeďte na prostředek veřejné IP adresy ze sady záznamů DNS A/AAAA.** Můžete vytvořit sadu záznamů a/AAAA a nastavit ji jako záznam aliasu, aby odkazoval na prostředek veřejné IP adresy.
- **Najeďte na profil Traffic Manager ze sady záznamů DNS A/AAAA/CNAME.** Můžete Ukázat na záznam CNAME profilu Traffic Manager ze sady záznamů DNS CNAME. Příkladem je contoso.trafficmanager.net. Nyní můžete také Ukázat na profil Traffic Manager, který obsahuje externí koncové body ze sady záznamů A nebo AAAA v zóně DNS.
- **Najeďte na koncový bod Azure Content Delivery Network (CDN)**. To je užitečné při vytváření statických webů s využitím služby Azure Storage a Azure CDN.
- **Najeďte na jinou sadu záznamů DNS ve stejné zóně.** Záznamy aliasů mohou odkazovat na jiné sady záznamů stejného typu. Můžete mít například sadu záznamů DNS CNAME nastavenou jako alias pro jinou sadu záznamů CNAME stejného typu. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů byly aliasy a některé jiné než aliasy.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Můžu vytvářet a aktualizovat záznamy aliasů z Azure Portal?

Ano. Můžete vytvářet nebo spravovat záznamy s aliasy v Azure Portal společně s rozhraními API Azure REST, PowerShellu, CLI a sady SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Povede záznamy aliasů k zajištění odstranění sady záznamů DNS při odstranění základní veřejné IP adresy?

Ano. Tato funkce je jednou ze základních funkcí záznamů aliasů. Pomáhá vyhnout se potenciálním výpadkům pro uživatele aplikace.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Poznáte záznamy aliasů, aby se zajistilo, že se sada záznamů DNS aktualizuje na správnou IP adresu při změně základní veřejné IP adresy?

Ano. Tato funkce je jednou ze základních funkcí záznamů aliasů. Pomáhá vyhnout se potenciálním výpadkům nebo bezpečnostním rizikům vaší aplikace.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existují nějaká omezení při použití sad záznamů aliasů pro záznamy AAAA a odkazují na Traffic Manager?

Ano. Aby odkazoval na profil Traffic Manager jako alias ze sady záznamů A nebo AAAA, Traffic Manager profil musí používat jenom externí koncové body. Při vytváření externích koncových bodů v Traffic Manager zadejte vlastní IP adresy koncových bodů.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Existují další poplatky za použití záznamů aliasů?

Záznamy aliasů jsou kvalifikací pro platnou sadu záznamů DNS. Pro záznamy aliasů se neúčtují žádné další fakturace.

## <a name="use-azure-dns"></a>Použít Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Je možné doménu hostovat pomocí Azure DNS a jiného poskytovatele DNS?

Ano. Azure DNS podporuje souběžné hostování domén s jinými službami DNS.

Chcete-li nastavit spoluhostování, upravte záznamy NS pro doménu tak, aby odkazovaly na názvové servery obou zprostředkovatelů. Záznamy názvového serveru (NS) určují, kteří zprostředkovatelé obdrží dotazy DNS pro doménu. Tyto záznamy NS můžete upravit v Azure DNS, v druhém poskytovateli a v nadřazené zóně. Nadřazená zóna je obvykle nakonfigurována prostřednictvím registrátora názvu domény. Další informace o delegování DNS najdete v tématu [delegování domény DNS](dns-domain-delegation.md).

Také se ujistěte, že jsou mezi poskytovateli DNS synchronizovány záznamy DNS pro tuto doménu. Azure DNS v současné době nepodporuje přenosy zón DNS. Záznamy DNS se musí synchronizovat pomocí [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [REST API](/rest/api/dns/), [sady SDK](dns-sdk.md), [rutin PowerShellu](dns-operations-recordsets.md)nebo [nástroje CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Je nutné delegovat doménu na všechny čtyři Azure DNS názvové servery?

Ano. Azure DNS přiřadí každé zóně DNS čtyři názvové servery. Toto uspořádání představuje izolaci chyb a zvýšenou odolnost. Pokud chcete získat nárok na smlouvu SLA pro Azure DNS, přejmenujte svoji doménu na všechny čtyři názvové servery.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jaká jsou omezení využití pro Azure DNS?

Při použití Azure DNS platí následující výchozí omezení.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Je možné přesunout zónu Azure DNS mezi skupinami prostředků nebo mezi předplatnými?

Ano. Zóny DNS se dají přesouvat mezi skupinami prostředků nebo mezi předplatnými.

Při přesunu zóny DNS nebude mít žádný vliv na dotazy DNS. Názvové servery přiřazené k zóně zůstávají stejné. Dotazy DNS se zpracovávají jako normální v celém rozsahu.

Další informace a pokyny, jak přesunout zóny DNS, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak dlouho trvá, než se změny DNS projeví?

Nové zóny DNS a záznamy DNS se obvykle zobrazují ve Azure DNS názvové servery. Časování je několik sekund.

Změny stávajících záznamů DNS můžou trvat trochu déle. Obvykle se zobrazují v Azure DNS názvových serverech do 60 sekund. Ukládání DNS do mezipaměti pro klienty DNS a rekurzivní překladače DNS mimo Azure DNS může ovlivnit také časování. K řízení této doby trvání mezipaměti použijte vlastnost TTL (Time to Live) každé sady záznamů.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak můžu chránit své zóny DNS před náhodným odstraněním?

Azure DNS se spravují pomocí Azure Resource Manager. Azure DNS výhody z funkcí řízení přístupu, které Azure Resource Manager poskytuje. Řízení přístupu na základě role v Azure se dá použít k řízení přístupu uživatelů ke čtení nebo zápisu do zón a sad záznamů DNS. Zámky prostředků zabraňují náhodnému úpravám nebo odstraňování zón a sad záznamů DNS.

Další informace najdete v tématu [ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Návody nastavit v Azure DNS záznamy SPF?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Řeší Azure DNS názvové servery přes IPv6? 

Ano. Azure DNS názvové servery jsou duální zásobníky. Duální zásobník znamená, že mají adresy IPv4 a IPv6. Pokud chcete najít adresu IPv6 pro Azure DNS názvové servery přiřazené k zóně DNS, použijte nástroj, jako je například Nslookup. Příklad: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Návody nastavit IDN v Azure DNS?

Mezinárodní názvy domén (IDN) zakódují jednotlivé názvy DNS pomocí [Punycode](https://en.wikipedia.org/wiki/Punycode). Dotazy DNS se provádějí pomocí těchto názvů kódovaných v Punycode.

Pokud chcete nakonfigurovat IDN v Azure DNS, převeďte název zóny nebo název sady záznamů na Punycode. Azure DNS v současné době nepodporuje integrovaný převod na nebo z Punycode.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o Azure DNS](dns-overview.md).

- [Přečtěte si další informace o použití Azure DNS u privátních domén](private-dns-overview.md).

- [Přečtěte si další informace o zónách a záznamech DNS](dns-zones-records.md).

- [Začínáme s Azure DNS](dns-getstarted-portal.md).
