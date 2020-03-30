---
title: Časté otázky – Azure DNS
description: V tomto článku se dozvíte o nejčastějších dotazech týkajících se Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121714"
---
# <a name="azure-dns-faq"></a>Nejčastější dotazy k Azure DNS

## <a name="about-azure-dns"></a>O Azure DNS

### <a name="what-is-azure-dns"></a>Co je Azure DNS?

Dns (Domain Name System) překládá nebo překládá název webu nebo služby na svou IP adresu. Azure DNS je hostitelská služba pro domény DNS. Poskytuje překlad názvů pomocí infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Domény DNS ve službě Azure DNS jsou hostované v globální síti názvových serverů DNS Azure. Tento systém používá síť Anycast, takže každý dotaz DNS je zodpovězen nejbližším dostupným serverem DNS. Azure DNS poskytuje rychlý výkon a vysokou dostupnost pro vaši doménu.

Azure DNS je založený na Azure Resource Manageru. Azure DNS těží z funkcí Správce prostředků, jako je řízení přístupu na základě rolí, protokoly auditování a uzamčení prostředků. Domény a záznamy můžete spravovat prostřednictvím portálu Azure, rutin Azure PowerShell a multiplatformního nastavení řízení o nastavení řízení on-line platformy. Aplikace, které vyžadují automatickou správu DNS, se mohou integrovat se službou prostřednictvím rozhraní REST API a sad SDK.

### <a name="how-much-does-azure-dns-cost"></a>Kolik stojí Azure DNS?

Model fakturace Azure DNS je založený na počtu zón DNS hostovaných ve službě Azure DNS. Je také založen na počtu dotazů DNS, které obdrží. Slevy jsou poskytovány na základě využití.

Další informace najdete na [stránce s cenami Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Co je smlouva SLA pro Azure DNS?

Azure zaručuje, že platné požadavky DNS obdrží odpověď alespoň z jednoho názvového serveru Azure DNS 100 % času.

Další informace najdete na [stránce Azure DNS SLA .](https://azure.microsoft.com/support/legal/sla/dns)

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co je zóna DNS? Je to stejné jako doména DNS? 

Doména je jedinečný název v systému názvů domén. Příklad: contoso.com.

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Například contoso.com domény může obsahovat několik záznamů DNS. Záznamy mohou obsahovat mail.contoso.com pro\.poštovní server a www contoso.com pro web. Tyto záznamy jsou hostovány v zóně DNS contoso.com.

Název domény je *pouze název*. Zóna DNS je datový prostředek, který obsahuje záznamy DNS pro název domény. Azure DNS můžete použít k hostování zóny DNS a správě záznamů DNS pro doménu v Azure. Poskytuje také názvové servery DNS pro zodpovězení dotazů DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Musím si koupit název domény DNS, abych mohl používat Azure DNS? 

Ne nutně.

Nemusíte kupovat doménu pro hostování zóny DNS v Azure DNS. Zónu DNS můžete kdykoli vytvořit bez vlastnictví názvu domény. Dotazy DNS pro tuto zónu se vyřeší pouze v případě, že jsou směrovány na názvové servery Azure DNS přiřazené k zóně.

Chcete-li propojit zónu DNS s globální hierarchií DNS, musíte si koupit název domény. Dotazy DNS odkudkoli na světě pak najdou vaši zónu DNS a odpoví pomocí dns záznamů.

## <a name="azure-dns-features"></a>Funkce Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existují nějaká omezení při použití aliasových záznamů pro vrchol názvu domény s Traffic Managerem?

Ano. Ve službě Azure Traffic Manager je nutné používat statické veřejné IP adresy. Nakonfigurujte cíl **externího koncového bodu** pomocí statické adresy IP. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Podporuje Azure DNS směrování provozu založené na DNS nebo převzetí služeb při selhání koncového bodu?

Směrování přenosů na základě DNS a převzetí služeb při selhání koncového bodu poskytuje Traffic Manager. Traffic Manager je samostatná služba Azure, kterou lze použít s Azure DNS. Další informace naleznete v přehledu [Traffic Manageru](../traffic-manager/traffic-manager-overview.md).

Azure DNS podporuje pouze hostování statických domén DNS, kde každý dotaz DNS pro daný záznam DNS vždy obdrží stejnou odpověď DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Podporuje Azure DNS registraci názvu domény?

Ne. Azure DNS momentálně nepodporuje možnost nákupu názvů domén. Chcete-li koupit domény, musíte použít registrátora názvů domén třetích stran. Registrátor obvykle účtuje malý roční poplatek. Domény pak můžete hostovat v Azure DNS pro správu záznamů DNS. Další informace najdete [v tématu Delegování domény na Azure DNS](dns-domain-delegation.md).

Funkce pro nákup názvů domén je sledována v nevyřízených položkách Azure. Pomocí webu pro zpětnou vazbu [zaregistrujte podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Podporuje Azure DNS DNSSEC?

Ne. Azure DNS aktuálně nepodporuje rozšíření zabezpečení DNSSEC (Domain Name System Extensions).

Funkce DNSSEC je sledována v nevyřízených položkách Azure DNS. Pomocí webu pro zpětnou vazbu [zaregistrujte podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Podporuje Azure DNS zónové přenosy (AXFR/IXFR)?

Ne. Azure DNS aktuálně nepodporuje zónové přenosy. Zóny DNS lze [importovat do Azure DNS pomocí azure cli](dns-import-export.md). Záznamy DNS se spravují prostřednictvím [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [rozhraní REST API](https://docs.microsoft.com/powershell/module/az.dns), sady [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md)nebo nástroje rozhraní [příkazového příkazu](dns-operations-recordsets-cli.md).

Funkce přenosu zóny je sledována v nevyřízených položkách Azure DNS. Pomocí webu pro zpětnou vazbu [zaregistrujte podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Podporuje Azure DNS přesměrování adres URL?

Ne. Služby přesměrování adres URL nejsou službou DNS. Pracují na úrovni HTTP, nikoli na úrovni DNS. Někteří poskytovatelé DNS sdružují službu přesměrování adres URL jako součást své celkové nabídky. Tato služba není aktuálně podporovaná Službou Azure DNS.

Funkce přesměrování adres URL je sledována v nevyřízených položkách Azure DNS. Pomocí webu pro zpětnou vazbu [zaregistrujte podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Podporuje Azure DNS rozšířené kódování ASCII (8bitové) nastavené pro sady záznamů TXT?

Ano. Azure DNS podporuje rozšířené ascii kódování nastavit pro sady záznamů TXT. Ale musíte použít nejnovější verzi Azure REST API, SDK, PowerShell a CLI. Verze starší než 1 října 2017 nebo SDK 2.1 nepodporují rozšířenou sadu ASCII. 

Můžete například zadat řetězec jako hodnotu záznamu TXT, který má rozšířený znak ASCII \128. Příkladem je "abcd\128efgh." Azure DNS používá bajt hodnotu tohoto znaku, což je 128, v interní reprezentaci. V době překladu DNS je tato hodnota bajtvrácena vrácena v odpovědi. Všimněte si také, že "abc" a "\097\098\099" jsou zaměnitelné, pokud jde o řešení. 

Řídíme se pravidly úniku hlavního formátu zóny [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) pro záznamy TXT. Například `\` nyní skutečně uniká vše na RFC. Pokud zadáte `A\B` hodnotu záznamu TXT, bude reprezentována `AB`a vyřešena pouze . Pokud opravdu chcete, aby záznam `A\B` TXT měl v `\` rozlišení, musíte znovu uniknout. Jako příklad zadejte `A\\B`.

Tato podpora momentálně není dostupná pro txt záznamy vytvořené z webu Azure Portal.

## <a name="alias-records"></a>Záznamy aliasů

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Jaké jsou některé scénáře, kde alias záznamy jsou užitečné?

Podívejte se na část scénářů v [přehledu záznamů aliasů Azure DNS](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jaké typy záznamů jsou podporovány pro sady záznamů aliasů?

Sady záznamů aliasu jsou podporovány pro následující typy záznamů v zóně Azure DNS:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jaké prostředky jsou podporovány jako cíle pro sady záznamů aliasu?

- **Přejděte na veřejný prostředek IP ze sady záznamů DNS A/AAAA.** Můžete vytvořit sadu záznamů A/AAAA a nastavit její alias, která bude ukazovat na veřejný prostředek IP.
- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME.** Můžete přejděte na cname profilu Traffic Manageru ze sady záznamů Dns CNAME. Příkladem je contoso.trafficmanager.net. Nyní můžete také překážet na profil Traffic Manageru, který má externí koncové body ze záznamu A nebo AAAA nastaveného v zóně DNS.
- **Přejděte na koncový bod sítě doručování obsahu Azure (CDN).** To je užitečné při vytváření statických webů pomocí azure storage a Azure CDN.
- **Přejděte na jiný záznam DNS nastavený ve stejné zóně.** Alias záznamy mohou odkazovat na jiné sady záznamů stejného typu. Můžete mít například sadu záznamů DNS CNAME nastavenou jako alias pro jinou sadu záznamů CNAME stejného typu. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů byly aliasy a některé nealiasy.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Můžu vytvářet a aktualizovat záznamy aliasů z webu Azure?

Ano. Můžete vytvořit nebo spravovat alias záznamy na webu Azure portal spolu s Azure REST API, PowerShell, CLI a Sady SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Pomůže záznamy aliasu zajistit odstranění sady záznamů DNS při odstranění podkladové veřejné IP adresy?

Ano. Tato funkce je jednou ze základních možností záznamů aliasů. Pomáhá vyhnout se potenciálním výpadkům pro uživatele vaší aplikace.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Pomůže záznamy aliasů zajistit, aby byla moje sada záznamů DNS při změně podkladové veřejné IP adresy aktualizována na správnou adresu IP?

Ano. Tato funkce je jednou ze základních možností záznamů aliasů. Pomáhá vám vyhnout se potenciálním výpadkům nebo bezpečnostním rizikům pro vaši aplikaci.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existují nějaká omezení při použití sad záznamů aliasu pro záznamy A nebo AAAA, které odkazují na Traffic Manager?

Ano. Chcete-li přejděte na profil traffic manageru jako alias ze sady záznamů A nebo AAAA, musí profil Traffic Manager používat pouze externí koncové body. Při vytváření externích koncových bodů ve Správci provozu zadejte skutečné IP adresy koncových bodů.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Je za použití záznamů aliasu účtován příplatek?

Záznamy aliasů jsou kvalifikací platné sady záznamů DNS. Neexistuje žádná další fakturace pro alias záznamy.

## <a name="use-azure-dns"></a>Použití Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Můžu spoluhostovat doménu pomocí Azure DNS a jiného poskytovatele DNS?

Ano. Azure DNS podporuje co-hosting domény s jinými službami DNS.

Chcete-li nastavit co-hosting, upravte záznamy NS pro doménu tak, aby ukazovaly na názvové servery obou poskytovatelů. Záznamy názvového serveru (NS) řídí, kteří poskytovatelé obdrží dotazy DNS pro doménu. Tyto záznamy NS můžete upravit v Azure DNS, v jiném zprostředkovateli a v nadřazené zóně. Nadřazená zóna je obvykle konfigurována prostřednictvím registrátora názvů domén. Další informace o delegování DNS naleznete v tématu [delegování domény DNS](dns-domain-delegation.md).

Také se ujistěte, že záznamy DNS pro doménu jsou synchronizovány mezi oběma poskytovateli DNS. Azure DNS momentálně nepodporuje přenosy zón DNS. Záznamy DNS musí být synchronizovány pomocí [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [rozhraní REST API](https://docs.microsoft.com/rest/api/dns/), sady [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md)nebo [nástroje příkazového příkazu](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Musím delegovat svou doménu na všechny čtyři názvové servery Azure DNS?

Ano. Azure DNS přiřadí každé zóně DNS čtyři názvové servery. Toto uspořádání je pro izolaci poruch a zvýšenou odolnost. Chcete-li se kvalifikovat pro službu Azure DNS SLA, delegujte svou doménu na všechny čtyři názvové servery.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jaké jsou limity využití Azure DNS?

Následující výchozí limity platí při použití Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Můžu přesunout zónu Azure DNS mezi skupinami prostředků nebo mezi předplatnými?

Ano. Zóny DNS lze přesouvat mezi skupinami prostředků nebo mezi předplatnými.

Při přesunutí zóny DNS není žádný vliv na dotazy DNS. Názvové servery přiřazené k zóně zůstanou stejné. Dotazy DNS jsou zpracovávány jako obvykle v celém textu.

Další informace a pokyny k přesunutí zón DNS naleznete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak dlouho trvá, než se změny DNS projeví?

Nové zóny DNS a záznamy DNS se obvykle rychle zobrazují na názvových serverech Azure DNS. Načasování je několik sekund.

Změny existujících záznamů DNS mohou trvat o něco déle. Obvykle se zobrazí na názvových serverech Azure DNS během 60 sekund. Ukládání DNS do mezipaměti klienty DNS a rekurzivní překladače DNS mimo Azure DNS může také ovlivnit časování. Chcete-li řídit dobu trvání této mezipaměti, použijte vlastnost Time-To-Live (TTL) každé sady záznamů.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak mohu chránit své zóny DNS před náhodným odstraněním?

Azure DNS se spravuje pomocí Azure Resource Manageru. Azure DNS těží z funkcí řízení přístupu, které poskytuje Azure Resource Manager. Ovládací prvky řízení přístupu založené na rolích, které uživatelé mají přístup pro čtení nebo zápis do zón DNS a sad záznamů. Uzamčení prostředků zabránit náhodné změny nebo odstranění zón DNS a sady záznamů.

Další informace naleznete v [tématu Ochrana zón a záznamů DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak nastavím záznamy SPF ve Službě Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Překládá se názvové servery Azure DNS přes IPv6? 

Ano. Názvové servery Azure DNS jsou dva zásobníky. Duální zásobník znamená, že mají adresy IPv4 a IPv6. Pokud chcete najít adresu IPv6 pro názvové servery Azure DNS přiřazené k zóně DNS, použijte nástroj, jako je nslookup. Příklad: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Jak nastavím IDN ve Službě Azure DNS?

Mezinárodní názvy domén (IDNs) kódují každý název DNS pomocí [punycode](https://en.wikipedia.org/wiki/Punycode). Dotazy DNS jsou prováděny pomocí těchto punycode kódované názvy.

Chcete-li nakonfigurovat IDNs v Azure DNS, převeďte název zóny nebo název sady záznamů na punycode. Azure DNS v současné době nepodporuje předdefinovaný převod do nebo z punycode.

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure DNS](dns-overview.md).

- [Přečtěte si další informace o tom, jak používat Azure DNS pro privátní domény](private-dns-overview.md).

- [Další informace o zónách dns a záznamech](dns-zones-records.md).

- [Začínáme s Azure DNS](dns-getstarted-portal.md).
