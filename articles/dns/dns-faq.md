---
title: Nejčastější dotazy k Azure DNS
description: Nejčastější dotazy k Azure DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: bb5c4d508344f391d610aeaa7e0be54a93c997dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080024"
---
# <a name="azure-dns-faq"></a>Nejčastější dotazy k Azure DNS

## <a name="about-azure-dns"></a>O službě Azure DNS

### <a name="what-is-azure-dns"></a>Co je Azure DNS?

V systému DNS (Domain Name) překládá nebo přeloží, název webu nebo služby k její IP adrese. Azure DNS je hostitelská služba určená pro domény DNS. Poskytuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

DNS domény v Azure DNS jsou hostované na Azure globální síť názvových serverů DNS. Tento systém využívá Anycast sítě tak, aby každý dotaz DNS je zodpoví co nejblíže k dispozici server DNS. Azure DNS poskytuje rychlý výkon a vysokou dostupnost vaší domény.

Azure DNS je založená na Azure Resource Manageru. Azure DNS využívá výhod Resource Manageru funkce, jako je řízení přístupu na základě rolí, protokoly auditu a zamknutí prostředků. Můžete spravovat doménách a záznamech prostřednictvím webu Azure portal, rutin prostředí Azure PowerShell a Azure CLI pro různé platformy. Aplikace, které vyžadují Automatická správa DNS můžete integrovat služby přes rozhraní REST API a sad SDK.

### <a name="how-much-does-azure-dns-cost"></a>Kolik stojí Azure DNS?

Model fakturace Azure DNS je podle počtu zónách DNS hostovaných v Azure DNS. Také vychází z počtu dotazů DNS, kterou obdrží. Slevy jsou k dispozici na základě využití.

Další informace najdete v tématu [stránce s cenami Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaká je smlouva SLA pro Azure DNS?

Azure zaručuje, že pro platné žádosti DNS přijetí odpovědi z nejméně jednoho názvových serverů Azure DNS 100 % času.

Další informace najdete v tématu [stránku smlouvy SLA pro Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co je zóna DNS? Je to to samé jako doména DNS? 

Doména je jedinečný název v domain name system. Příklad: contoso.com.

K hostování záznamů DNS v určité doméně se používá zóna DNS. Například doména contoso.com může obsahovat několik záznamů DNS. Záznamy můžou obsahovat mail.contoso.com pro poštovní server a www\.contoso.com pro web. Tyto záznamy jsou hostované v zóně DNS contoso.com.

Název domény je *pouze název*. Zóny DNS je zdroj dat obsahující záznamy DNS pro název domény. Azure DNS můžete použít k hostování zóny DNS a správě záznamů DNS pro doménu v Azure. Taky poskytuje názvové servery DNS k odpovědi na dotazy DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Je potřeba zakoupit název domény DNS k použití Azure DNS? 

Ne nutně.

Koupit doménu k hostování zóny DNS v Azure DNS nemusíte. Zónu DNS můžete vytvořit kdykoli a nemusíte přitom vlastnit název příslušné domény. Dotazy DNS pro tuto zónu vyřešit pouze pokud jste přesměrováni na názvové servery Azure DNS přiřadit k zóně.

Pokud chcete svoji zónu DNS propojit do globální hierarchií DNS, je nutné zakoupit název domény. Potom DNS dotazuje odkudkoli na světě najít odpovědi pomocí svoje DNS záznamy a zóny DNS.

## <a name="azure-dns-features"></a>Funkce Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existují nějaká omezení při použití záznamů aliasů pro název vrcholu domény s Traffic Managerem?

Ano. S Azure Traffic Managerem musíte použít statické veřejné IP adresy. Konfigurace **externí koncový bod** cíl s použitím statické IP adresy. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS podporuje provozu na základě DNS směrování nebo koncový bod převzetí služeb při selhání?

Traffic Managerem provozu na základě DNS koncového bodu a směrování převzetí služeb při selhání jsou k dispozici. Traffic Manager je samostatná služba Azure, který lze použít s Azure DNS. Další informace najdete v tématu [Traffic Manager – Přehled](../traffic-manager/traffic-manager-overview.md).

Azure DNS podporuje pouze statické domény DNS, kde každý dotaz DNS pro daný záznam DNS vždycky obdržel stejnou odpověď DNS hostování.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS podporuje registrací názvu domény?

Ne. Azure DNS v současné době nepodporuje možnost koupit si názvy domén. Koupit domény, musíte použít registrátora názvu domén třetí strany. Doménový Registrátor obvykle účtuje malý roční poplatek. Domény pak může být hostovaná v Azure DNS pro správu záznamů DNS. Další informace najdete v tématu [Delegování domény do DNS Azure](dns-domain-delegation.md).

Funkci tak, aby nákup názvů domén je sledována v Azure nevyřízených položek. Použití webu zpětné vazby k [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS podporuje DNSSEC?

Ne. Azure DNS v současné době nepodporuje domény Name System Security Extensions (DNSSEC).

Funkce DNSSEC jsou sledovány v nevyřízených položkách Azure DNS. Použití webu zpětné vazby k [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS podporuje přenosy zóny (AXFR a IXFR)?

Ne. Azure DNS v současné době nepodporuje přenosy zóny. Zóny DNS můžou být [importovat do Azure DNS pomocí rozhraní příkazového řádku Azure](dns-import-export.md). Záznamy DNS jsou spravované prostřednictvím [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [rozhraní REST API](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md), nebo [ Nástroj příkazového řádku](dns-operations-recordsets-cli.md).

Funkce přenosu zóny jsou sledovány v nevyřízených položkách Azure DNS. Použití webu zpětné vazby k [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS podporuje přesměrování na adresu URL?

Ne. Adresa URL přesměrování služby nejsou služby DNS. Fungují na úrovni protokolu HTTP, nikoli na úrovni DNS. Někteří poskytovatelé DNS sady adresu URL přesměrování služby jako součást své celkové nabídky. Tato služba se aktuálně nepodporuje Azure DNS.

Funkce přesměrování URL jsou sledovány v nevyřízených položkách Azure DNS. Použití webu zpětné vazby k [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS podporuje rozšířené ASCII (8 bitů) sady pro sady záznamů TXT kódování?

Ano. Azure DNS podporuje rozšířené ASCII kódování sady pro sady záznamů TXT. Ale je nutné použít nejnovější verzi rozhraní REST API služby Azure, sady SDK, prostředí PowerShell a rozhraní příkazového řádku. Verze starší než 1. října 2017, nebo sadu SDK 2.1 nepodporují rozšířené sady ASCII. 

Můžete například zadat řetězec jako hodnotu pro záznam TXT, který má rozšířené \128 znak ASCII. Příkladem je "abcd\128efgh." Azure DNS používá hodnotu bajtu tohoto znaku, který je 128, v interní reprezentace. V době překlad názvů DNS je vrácena tato hodnota bajtu v odpovědi. Všimněte si také, že "abc" a "\097\098\099" jsou zaměnitelné jde řešení. 

Budeme postupovat podle [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zóna pravidla řídicí hlavní formát souboru pro záznamy TXT. Například `\` nyní skutečně řídicí sekvence všechno za specifikaci RFC. Pokud zadáte `A\B` jako hodnotu záznamu TXT, má reprezentované a vyřešeno jako sdílení `AB`. Pokud chcete záznam TXT, který chcete mít `A\B` rozlišením, budete muset escape `\` znovu. Například zadejte `A\\B`.

Tato podpora aktuálně není k dispozici pro záznamy TXT vytvořené na webu Azure Portal.

## <a name="alias-records"></a>Záznamy aliasů

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Co jsou uvedeny některé scénáře, kde záznamů aliasů jsou užitečné?

Viz scénáře tématu [Azure DNS alias záznamy přehled](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jaké typy záznamu jsou podporovány pro sady záznamů aliasů?

Sady záznamů aliasů jsou podporovány pro následující typy záznamů v zóně Azure DNS:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Které prostředky jsou podporovány jako cíle pro sady záznamů alias?

- **Odkazovat na prostředek veřejné IP adresy ze serveru DNS A/AAAA sadu záznamů.** Můžete vytvořit sadu záznamů A/AAAA a nastavte ji alias záznamu sady tak, aby odkazoval na prostředek veřejné IP adresy.
- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME.** Ze sady záznamů DNS CNAME může odkazovat na CNAME profil služby Traffic Manager. Příkladem je contoso.trafficmanager.net. Teď budete také může odkazovat na profil Traffic Manageru, který má externí koncové body z záznam A nebo AAAA, nastavte ve vaší zóně DNS.
- **Přejděte na koncový bod Azure Content Delivery Network (CDN)** . To je užitečné při vytváření statických webů pomocí služby Azure storage a Azure CDN.
- **Přejděte na jinou sadu záznamů DNS v rámci stejné zóny.** Záznamů aliasů můžete odkazovat na jiné sady záznamů stejného typu. Můžete mít například sadu záznamů DNS CNAME nastavenou jako alias pro jinou sadu záznamů CNAME stejného typu. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů bude aliasy a některé jiné aliasy.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Můžete vytvořit a aktualizovat alias záznamy z portálu Azure portal?

Ano. Můžete vytvořit nebo spravovat záznamů aliasů na webu Azure Portal spolu s rozhraní Azure REST API, Powershellu, rozhraní příkazového řádku a sady SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Záznamů aliasů pomůže zajistit, aby že můj sady záznamů DNS se odstraní při odstranění základní veřejnou IP adresu?

Ano. Tato funkce je jedním z hlavních schopností v záznamů aliasů. Pomůže vám vyhnout potenciálním výpadkům pro uživatele vaší aplikace.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Alias se zaznamenává nápovědy, abyste měli jistotu, že moje sady záznamů DNS se aktualizuje na správnou IP adresu při změně podkladového veřejnou IP adresu?

Ano. Tato funkce je jedním z hlavních schopností v záznamů aliasů. Pomůže vám vyhnout potenciálním výpadkům nebo bezpečnostní rizika pro vaši aplikaci.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existují nějaká omezení při použití záznam aliasu sad pro záznamy A nebo AAAA tak, aby odkazovala na Traffic Manager?

Ano. Přejděte na profil Traffic Manageru jako alias z A nebo AAAA sady záznamů, Traffic Manager musí používat profil výhradně externími koncovými body. Když vytvoříte externí koncové body Traffic Manageru, zadejte skutečné IP adresy koncových bodů.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Jsou nějaké další poplatky za použití záznamů aliasů?

Záznamů aliasů jsou kvalifikaci na platnou sadu záznamů DNS. Není k dispozici žádné další fakturace za záznamů aliasů.

## <a name="use-azure-dns"></a>Použití Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Je možné společné hostování domény s využitím Azure DNS a jiného poskytovatele DNS?

Ano. Azure DNS podporuje společné hostování domény s jinými službami DNS.

Nastavit společné hostování, upravte záznamy NS pro doménu tak, aby odkazoval na názvové servery z obou poskytovatelů. Název serveru (NS) zaznamenává ovládací prvek poskytovatelů, kteří obdrží dotazy DNS pro doménu. Můžete upravit tyto záznamy NS v Azure DNS, v rámci zprostředkovatele a v nadřazené zóně. Nadřazené zóny se obvykle konfiguruje prostřednictvím registrátora názvu domény. Další informace o delegování DNS najdete v tématu [delegování DNS domény](dns-domain-delegation.md).

Také se ujistěte, jestli jsou synchronizované mezi oběma poskytovatelů DNS záznamy DNS pro doménu. Azure DNS v současné době nepodporuje přenosy zóny DNS. Záznamy DNS musí být synchronizovány s použitím buď [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [rozhraní REST API](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md), nebo [Nástroj rozhraní příkazového řádku](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Je nutné Delegovat doménu na všechny čtyři názvové servery Azure DNS?

Ano. Čtyři názvové servery Azure DNS přiřadí ke každé zóně DNS. Toto uspořádání se pro izolaci chyb a zvýšení pružnosti. K získání způsobilosti pro smlouvy SLA pro Azure DNS, delegování domény na všechny čtyři názvové servery.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jaká jsou omezení používání pro Azure DNS?

Následující výchozí omezení platí při použití Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Můžete přesouvat mezi skupinami prostředků nebo předplatnými zóny DNS?

Ano. Zóny DNS je možné přesunout mezi skupinami prostředků nebo předplatnými.

Při přesunu zónu DNS neexistuje žádný vliv na DNS dotazy. Názvových serverů přiřazených zóně zůstat stejná. Jako za normálních okolností v průběhu zpracování dotazů DNS.

Další informace a pokyny o tom, jak přesunout zóny DNS najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak dlouho trvá DNS se změny projeví?

Nové zóny DNS a záznamy DNS obvykle se zobrazí v názvové servery Azure DNS rychlé. Časování je pár sekund.

Změny v existujících záznamů DNS můžou trvat déle. Jsou obvykle zobrazena v Azure DNS názvové servery, které během 60 sekund. Ukládání do mezipaměti DNS tak, že klienti DNS a DNS rekurzivní překladače mimo Azure DNS také může ovlivnit načasování. Řízení v této hodnotě duration mezipaměti, použijte vlastnost Time To Live (TTL) každá sada záznamů.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak můžete chránit své zóny DNS před náhodným odstraněním?

Azure DNS spravuje pomocí Azure Resource Manageru. Azure DNS využívá výhod funkce řízení přístupu, které poskytuje Azure Resource Manageru. Řízení přístupu na základě rolí řídí, kteří uživatelé mají čtení nebo zápis do sady záznamů a zón DNS. Zámky prostředků zabránit náhodnému úpravy nebo odstranění sad záznamů a zón DNS.

Další informace najdete v tématu [záznamy a zóny DNS chránit](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak můžu nastavit záznamy SPF ve službě Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Vyřešit názvové servery Azure DNS přes protokol IPv6? 

Ano. Názvové servery Azure DNS jsou duálním zásobníkem. Duálním zásobníkem znamená, že mají IPv4 a IPv6 adresy. Najít adresu IPv6 pro Azure DNS názvové servery přiřazené pro vaší zónu DNS, použijte nástroj, jako je například nslookup. Příklad: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Jak můžu nastavit IDN ve službě Azure DNS?

Mezinárodní názvy domén (IDN) kódovat každý název DNS s využitím [kódování punycode](https://en.wikipedia.org/wiki/Punycode). Dotazy DNS jsou vytvářeny pomocí tyto názvy kódováním punycode.

Ke konfiguraci IDN ve službě Azure DNS, převeďte na kódování punycode název zóny nebo název sady záznamů. Azure DNS v současné době nepodporuje předdefinovaný převod do nebo z kódování punycode.

## <a name="next-steps"></a>Další postup

- [Další informace o službě Azure DNS](dns-overview.md).

- [Další informace o tom, jak používat Azure DNS pro privátní domény](private-dns-overview.md).

- [Další informace o DNS zóny a záznamy](dns-zones-records.md).

- [Začínáme s Azure DNS](dns-getstarted-portal.md).
