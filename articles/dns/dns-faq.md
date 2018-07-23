---
title: Nejčastější dotazy k Azure DNS | Dokumentace Microsoftu
description: Nejčastější dotazy k Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: victorh
ms.openlocfilehash: 747b2e2499a9bafcf7a7b03bc2ce144828c55c75
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172496"
---
# <a name="azure-dns-faq"></a>Nejčastější dotazy k Azure DNS

## <a name="about-azure-dns"></a>O službě Azure DNS

### <a name="what-is-azure-dns"></a>Co je Azure DNS?

Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese. Azure DNS je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

DNS domény v Azure DNS jsou hostované na Azure pro globální síť názvových serverů DNS. Tato funkce využívá Anycast sítě tak, aby každý dotaz DNS je zodpoví co nejblíže k dispozici server DNS. Azure DNS poskytuje rychlý výkon a vysokou dostupnost vaší domény.

Služba Azure DNS je založená na Azure Resource Manageru. V důsledku toho těží z funkce Správce prostředků, jako je řízení přístupu na základě rolí, protokoly auditu a zamknutí prostředků. Doménách a záznamech, je možné spravovat prostřednictvím webu Azure portal, rutin Azure Powershellu a Azure CLI pro různé platformy. Aplikace, které potřebují Automatická správa DNS můžete integrovat služby přes rozhraní REST API a sad SDK.

### <a name="how-much-does-azure-dns-cost"></a>Kolik stojí Azure DNS?

Model fakturace Azure DNS je podle počtu zónách DNS hostovaných v Azure DNS a počtu dotazů DNS, kterou obdrží. Slevy jsou k dispozici na základě využití.

Další informace najdete v tématu [stránce s cenami Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaká je smlouva SLA pro Azure DNS?

Azure zaručuje, že pro platné žádosti DNS přijetí odpovědi z nejméně jednoho názvových serverů Azure DNS minimálně 99,99 % času.

Další informace najdete v tématu [stránku smlouvy SLA pro Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co je zóny DNS? Je to to samé jako doména DNS? 

Doména je jedinečný název v domain name system, například "contoso.com".


K hostování záznamů DNS v určité doméně se používá zóna DNS. Třeba doména "contoso.com" může obsahovat několik záznamů DNS, třeba "mail.contoso.com" (pro poštovní server) a "www.contoso.com" (pro webový server). Tyto záznamy by byly umístěny v zóně DNS "contoso.com".

Název domény je *pouze název*, zatímco zóny DNS je zdroj dat obsahující záznamy DNS pro název domény. Azure DNS vám umožňuje hostovat zónu DNS a spravovat záznamy DNS pro doménu v Azure. Taky poskytuje názvové servery DNS k odpovědi na dotazy DNS z Internetu.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Když chci používat Azure DNS, musím si kupovat název domény DNS? 

Ne nutně.

K hostování zóny DNS v Azure DNS nemusíte kupovat doménu. Zónu DNS můžete vytvořit kdykoli a nemusíte přitom vlastnit název příslušné domény. Dotazy DNS pro tuto zónu pouze vyřeší, pokud jsou směrované na názvové servery Azure DNS přiřadit k zóně.

Budete muset zakoupit název domény, pokud chcete svoji zónu DNS propojit do globální hierarchií DNS – toto propojení umožní dotazy DNS odkudkoli na světě najít vaši zónu DNS a jako odpověď získat vaše záznamy DNS.

## <a name="azure-dns-features"></a>Funkce Azure DNS

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS podporuje provozu na základě DNS směrování nebo koncový bod převzetí služeb při selhání?

Azure Traffic Managerem provozu na základě DNS koncového bodu a směrování převzetí služeb při selhání jsou k dispozici. Azure Traffic Manager je samostatná služba Azure, který lze použít společně s Azure DNS. Další informace najdete v tématu [Traffic Manager – Přehled](../traffic-manager/traffic-manager-overview.md).

Azure DNS podporuje pouze hostování 'static' domény DNS, kde každý dotaz DNS pro daný záznam DNS vždycky obdržel stejnou odpověď DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS podporuje registrací názvu domény?

Ne. Azure DNS aktuálně nepodporuje nákup názvů domén. Pokud chcete zakoupit domén, budete muset použít registrátora názvu domén třetí strany. Doménový Registrátor obvykle účtuje malý roční poplatek. Domény pak se dají hostovat v Azure DNS pro správu záznamů DNS. Zobrazit [delegování domény do Azure DNS](dns-domain-delegation.md) podrobnosti.

Koupit domény je funkce sledována v Azure nevyřízených položek. Můžete použít web pro zasílání názorů na [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS podporuje DNSSEC?

Ne. Azure DNS v současné době nepodporuje DNSSEC.

DNSSEC je funkce sledován v nevyřízených položkách Azure DNS. Můžete použít web pro zasílání názorů na [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS podporuje přenosy zóny (AXFR a IXFR)?

Ne. Azure DNS v současné době nepodporuje přenosy zóny. Zóny DNS můžou být [importovat do Azure DNS pomocí Azure CLI](dns-import-export.md). Záznamy DNS je pak možné spravovat prostřednictvím [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), naše [rozhraní REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md), nebo [Nástroj rozhraní příkazového řádku](dns-operations-recordsets-cli.md).

Přenos zóny je funkce sledován v nevyřízených položkách Azure DNS. Můžete použít web pro zasílání názorů na [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS podporuje přesměrování na adresu URL?

Ne. Adresa URL pro přesměrování služby nejsou ve skutečnosti služba DNS - fungují na úrovni protokolu HTTP, nikoli na úrovni DNS. Někteří poskytovatelé DNS k vytvoření balíčku sady adresu URL přesměrování služby jako součást své celkové nabídky. To není podporováno aktuálně Azure DNS.

Adresa URL pro přesměrování funkce jsou sledovány v nevyřízených položkách Azure DNS. Můžete použít web pro zasílání názorů na [zaregistrovat vaše podpora pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Azure DNS podporuje rozšířené ASCII (8 bitů) sady pro sadu záznamů TXT kódování?

Ano. Azure DNS podporuje rozšířené sady kódování ASCII pro sady záznamů TXT, pokud používáte nejnovější verzi rozhraní Azure REST API, sady SDK, PowerShell a rozhraní příkazového řádku (verze starší než 2017-10-01 nebo sady SDK 2.1 se nepodporuje rozšířené sady ASCII). Například pokud uživatel zadá řetězec jako hodnotu na záznam TXT, který má rozšířené \128 znak ASCII (třeba: "abcd\128efgh"), Azure DNS bude používat hodnotu bajtu tohoto znaku (což je 128) v interní reprezentace. V době překlad názvů DNS i tento bajt bude vrácena hodnota v odpovědi. Všimněte si také, že "abc" a "\097\098\099" jsou zaměnitelné jde řešení. 

Budeme postupovat podle [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zóna pravidla řídicí hlavní formát souboru pro záznamy TXT. Například "\" nyní skutečně řídicí sekvence všechno za specifikaci RFC. Pokud zadáte jako hodnotu záznamu TXT "A\B.", budou mít stejné a vyřešit právě "AB". Pokud chcete záznam TXT mít "A\B" rozlišením, budete muset řídicí "\" znovu, tj. zadejte jako "A\\B". 

Všimněte si, že tato podpora není aktuálně k dispozici pro záznamy TXT vytvořené na webu Azure Portal. 

## <a name="using-azure-dns"></a>Použití Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Je možné společné hostování domény pomocí Azure DNS a jiného poskytovatele DNS?

Ano. Azure DNS podporuje společné hostování domény s jinými službami DNS.

Uděláte to tak, budete muset upravit záznamy NS pro doménu (který ovládací prvek poskytovatelů, kteří obdrží DNS dotazuje na doméně) tak, aby odkazoval na názvové servery z obou poskytovatelů. Můžete upravit tyto záznamy NS na třech místech: v Azure DNS, v rámci zprostředkovatele a v nadřazené zóně (většinou nakonfigurovaná prostřednictvím registrátora názvu domény). Další informace o delegování DNS najdete v tématu [delegování DNS domény](dns-domain-delegation.md).

Kromě toho je potřeba zajistit, že jsou synchronizované mezi oběma poskytovatelů DNS záznamy DNS pro doménu. Azure DNS v současné době nepodporuje přenosy zóny DNS. Záznamy DNS se musí synchronizovat pomocí [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [rozhraní REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [rutin prostředí PowerShell](dns-operations-recordsets.md), nebo [Nástroj rozhraní příkazového řádku](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Je nutné Delegovat doménu na všechny čtyři názvové servery Azure DNS?

Ano. Azure DNS přiřadí každé zóny DNS pro izolaci chyb a zvýšení pružnosti čtyři názvové servery. K získání způsobilosti pro smlouvy SLA pro Azure DNS, musíte Delegovat doménu do všechny čtyři názvové servery.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jaká jsou omezení používání pro Azure DNS?

Následující výchozí omezení platí při používání Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Můžete přesouvat mezi skupinami prostředků nebo předplatnými zóny DNS?

Ano. Zóny DNS je možné přesunout mezi skupinami prostředků nebo předplatnými.

Neexistuje žádný vliv na DNS dotazy, při přesouvání zónu DNS. Názvových serverů přiřazených zóně zůstávají stejné a jako za normálních okolností v průběhu zpracování dotazů DNS.

Další informace a pokyny o tom, jak přesunout zóny DNS najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak dlouho trvá DNS se změny projeví?

Nové zóny DNS a záznamy DNS jsou obvykle projeví na názvové servery Azure DNS rychlé, během několika sekund.

Změny v existujících záznamů DNS může trvat o něco déle, ale měli stále odrazí se na názvové servery Azure DNS během 60 sekund. V takovém případě DNS ukládání do mezipaměti na mimo Azure DNS (klienty DNS a rekurzivní překladače DNS) může také ovlivnit čas potřebný pro změnu DNS platit. V této hodnotě duration mezipaměti se dá řídit pomocí vlastnosti Time To Live (TTL) každá sada záznamů.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak můžete chránit své zóny DNS před náhodným odstraněním?

Azure DNS spravuje pomocí Azure Resource Manageru a výhody řízení přístupu funkce, že Azure Resource Manager poskytuje. Řízení přístupu na základě rolí umožňuje řídit, kteří uživatelé mají přečíst nebo oprávnění k zápisu do zóny DNS a sad záznamů. Zámky prostředků můžete použít pro zabránění náhodnému úpravy nebo odstranění zóny DNS a sad záznamů.

Další informace najdete v tématu [záznamy a zóny DNS ochrana](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak můžu nastavit záznamy SPF ve službě Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Vyřešit názvové servery Azure DNS přes protokol IPv6? 

Ano. Názvové servery Azure DNS jsou duální sadou protokolů (mají adresy IPv4 a IPv6). Najít adresu IPv6 pro Azure DNS názvové servery přiřazené pro vaší zónu DNS, můžete použít nástroj, jako je například nslookup (například `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Jak nastavit až mezinárodních názvů domén (IDN) v Azure DNS?

Mezinárodní názvy domén (IDN) fungují tak, že každý název DNS pomocí kódování "[kódování punycode](https://en.wikipedia.org/wiki/Punycode)". Dotazy DNS se sestavují pomocí tyto názvy kódováním punycode.

Mezinárodní názvy domén (IDN) v Azure DNS můžete nakonfigurovat první převedením název zóny nebo názvem sady záznamů na kódování punycode. Azure DNS v současné době nepodporuje předdefinovaný převod do a z kódování punycode.

## <a name="private-dns"></a>Privátní DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS podporuje 'private' domény?
Podpora domén 'private' je implementováno pomocí funkce privátní zóny.  Tato funkce je aktuálně dostupná ve verzi public preview.  Privátních zón se spravují pomocí stejných nástrojů jako Azure DNS zón směřujících k Internetu, ale pouze se dá přeložit z v rámci zadaného virtuálních sítích.  Zobrazit [přehled](private-dns-overview.md) podrobnosti.

V tuto chvíli nepodporuje privátní zóny na webu Azure portal. 

Informace o dalších interní možnosti služby DNS v Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Jaký je rozdíl mezi registrační virtuální síť a virtuální síť pro překlad v rámci privátních zón? 
Propojení virtuálních sítí pro privátní zóny DNS dvěma způsoby – jako registrační virtuální síť nebo jako virtuální síť pro překlad. V obou případech se bude moct úspěšně přeložil záznamy v privátní zóny virtuálními počítači ve virtuální síti. Pokud však zadáte virtuální sítě jako registrační virtuální síť, Azure bude automaticky zaregistrovat záznamů DNS (dynamické registrace) do zóny pro virtuální počítače ve virtuální síti. Kromě toho, když virtuální počítač v registraci se odstraní virtuální síť, Azure také automaticky odebere odpovídající záznam DNS z propojené privátní zónu. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Bude Azure DNS Private Zones fungovat v různých oblastech Azure?
Ano. Privátní zóny se podporuje pro překlad názvů DNS mezi virtuálními sítěmi v různých oblastech Azure i bez explicitně partnerský vztah virtuálních sítí tak dlouho, dokud virtuální sítě jsou určeny jako virtuální sítě pro překlad pro privátní zóny. Možná budou zákazníci virtuální sítě vytvořit partnerský vztah pro provoz TCP/HTTP mají být předány z jedné oblasti. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Je připojení k Internetu z virtuální sítě požadované pro privátní zóny?
Ne. Privátní zóny fungování ve spojení s virtuálními sítěmi a umožněte zákazníkům spravovat domény pro virtuální počítače nebo jiné prostředky v rámci a mezi virtuálními sítěmi. Bez připojení k Internetu je vyžadován pro překlad názvů. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Je možné stejnou privátní zónu pro více virtuálních sítí pro překlad? 
Ano. Zákazníky můžete přidružit až 10 virtuální sítě pro překlad jednu privátní zónu.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Virtuální síť, která patří do jiného předplatného lze jako virtuální síť pro překlad do privátních zón? 
Ano, za předpokladu, uživatel má oprávnění k zápisu operace na obou virtuálních sítí a také zóny privátního DNS. Všimněte si, že může být přiděleno oprávnění k zápisu do více rolí RBAC. Například role RBAC Přispěvatel klasické sítě má oprávnění k zápisu do virtuální sítě. Další informace o rolích RBAC najdete v tématu [řízení přístupu na základě rolí](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Záznamy DNS automaticky registrované virtuální počítač v privátní zóny se automaticky odstraní při odstranění virtuální počítače zákazníka?
Ano. Pokud odstraníte virtuální počítač v rámci registrační virtuální síť, bude automaticky mažeme záznamy DNS, které jste zaregistrovali do zóny, protože nejsou registrační virtuální síť. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Můžete být záznam automaticky registrované virtuální počítač v privátní zóny (z registrační virtuální síť) ručně odstranit? 
Ne. V tuto chvíli nejsou záznamů DNS virtuálního počítače, které jsou automaticky registruje v privátní zóny z registrační virtuální síť viditelné nebo upravovat zákazníky. Ale můžete nahradit (její přepsání) zaznamenat tyto automaticky registrované záznamů DNS pomocí ručně vytvořeného DNS v zóně. Podívejte se následující otázky a odpovědi, adresující to.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Co se stane, když se pokusíme ručně vytvořit nový záznam DNS do privátní zóny, který má stejný název hostitele jako (automaticky registrovaných) existujícího virtuálního počítače v registrační virtuální síť? 
Pokud se pokusíte ručně vytvořit nový záznam DNS do privátní zóny, který má stejný název hostitele jako existující virtuální počítač (automaticky registrovaných) v registrační virtuální síť, povolíme nový záznam DNS pro přepsání automaticky zapsané záznam virtuálního počítače. Kromě toho následně při pokusu odstranit tento ručně vytvořit záznam DNS v zóně, bude úspěšné odstranění a automatické registrace dojde znovu (záznam DNS bude znovu vytvořit automaticky v zóně) Pokud je to virtuální počítač stále existuje a má privátní IP Adresou k němu připojená. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Co se stane, když jsme odpojit registrační virtuální síť z privátní zóny? Záznamy automaticky registrované virtuálního počítače z virtuální sítě odebrané ze zóny i?
Ano. Pokud zrušíte propojení registrační virtuální síť (aktualizace zóny DNS odebrat přidružené registrační virtuální síť) z privátní zónu, Azure odebere záznamy, které automaticky registrované virtuálního počítače ze zóny. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Co se stane, když jsme odstranit registrace (nebo řešení) virtuální síť, která je propojené s privátní zóny? Máme ručně aktualizovat privátní zóny na zrušení propojení virtuální sítě jako registrace (nebo řešení) virtuální sítě v zóně?
Ano. Když odstraníte virtuální síť registrace (nebo řešení) bez oddělení od privátní zóna nejprve, Azure vám umožní vaší operace odstranění úspěšná, ale virtuální sítě není automaticky odpojit z vaší privátní zóny případné. Budete muset ručně zrušit propojení virtuální sítě z privátní zónu. Z tohoto důvodu doporučujeme nejprve zrušit propojení virtuální sítě z vaší privátní zóny před jeho odstraněním.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>By překlad DNS pomocí výchozího plně kvalifikovaný název domény (internal.cloudapp.net) i nadále fungovat i v případě, že privátní zónu (Příklad: contoso.local) je propojený s virtuální sítí? 
Ano. Privátní zóny funkce nenahrazuje rozlišení DNS výchozí využívající tuto zónu internal.cloudapp.net poskytuje Azure a nabízí se jako další funkce nebo vylepšení. Pro oba případy (ať už spoléhat na internal.cloudapp.net poskytuje Azure nebo na vlastní privátní zónu) doporučujeme použít plně kvalifikovaný název domény chcete, aby se přeložil zóny. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>By příponu DNS na virtuální počítače v rámci virtuální sítě propojené změnit tak, aby u privátních zón? 
Ne. V tuto chvíli příponu DNS na virtuální počítače ve vaší virtuální sítě propojené zůstanou jako výchozí příponou poskytuje Azure ("*. internal.cloudapp.net"). Můžete však ručně změnit tuto příponu DNS na virtuálních počítačích na, privátní zónu. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Existují nějaká omezení pro privátní zóny během tohoto období preview?
Ano. Ve verzi Public Preview existují následující omezení:
* 1 registračních virtuálních sítí na privátních zón
* Až 10 řešení virtuálních sítí na jednu privátní zónu
* Dané virtuální síti můžete propojit jen na jednu privátní zónu jako registrační virtuální síť
* Až 10 privátní zóny lze propojit dané virtuální síti jako virtuální síť pro překlad
* Pokud je zadaný registrační virtuální síť, záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrované na privátních zón nesmí být zobrazitelná nebo získat z Powershellu nebo rozhraní příkazového řádku/rozhraní API, ale záznamy virtuálního počítače jsou ve skutečnosti zaregistrované a vyřeší úspěšně
* Reverzní DNS bude fungovat jenom pro adresní prostor privátní IP v registrační virtuální síť
* Zpětné vyhledávání DNS pro privátní IP Adresou, která není registrována v privátní zónu (Příklad: privátní IP pro virtuální počítač ve virtuální síti, který se propojí jako řešení virtuální sítě pro privátní zóny) vrátí "internal.cloudapp.net" jako přípona DNS, ale Tato přípona nesmí být možné přeložit.   
* Virtuální síť musí být prázdná (např.) žádné virtuální počítače s síťové rozhraní připojené) při počátečním (např.) prvním) propojení privátních zón jako rozlišení nebo registrační virtuální síť. Však může být virtuální síť pak neprázdný pro budoucí propojení jako virtuální síť registraci nebo řešení, k jiné privátní zóny. 
* V tuto chvíli podmíněné předávání není podporována, například pro povolení rozlišení mezi sítěmi Azure a místního prostředí. Dokumentaci k jak umožňuje zákazníkům realizovat tento scénář prostřednictvím jiné mechanismy, najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Existují všechny kvóty nebo limity na zóny a záznamy pro privátní zóny?
Nejsou žádná zvláštní omezení počtu zón pro předplatné povolené nebo počet sad záznamů na zóny pro privátní zóny. Veřejné a privátní zóny počítají na celkové limity DNS popsané [zde](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Je k dispozici podpora portál pro privátní zóny?
Privátní zóny, které jsou již vytvořených prostřednictvím mechanismy mimo portál (rozhraní API nebo PowerShell nebo rozhraní příkazového řádku/sad SDK) se nebude zobrazovat na portálu Azure portal, ale zákazníci budou moci vytvořit nový privátní zóny nebo spravovat přidružení s virtuálními sítěmi. Kromě toho pro virtuální sítě přidružený jako registrační virtuální sítě, automaticky registrované záznamy virtuálního počítače nezobrazí na portálu. 

## <a name="next-steps"></a>Další postup

[Další informace o službě Azure DNS](dns-overview.md)
<br>
[Další informace o používání Azure DNS pro privátní domény](private-dns-overview.md)
<br>
[Další informace o záznamy a zóny DNS](dns-zones-records.md)
<br>
[Začínáme s Azure DNS](dns-getstarted-portal.md)

