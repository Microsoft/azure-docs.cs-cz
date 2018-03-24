---
title: Nejčastější dotazy k Azure DNS | Microsoft Docs
description: Časté otázky k Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: f07f914ccf8ea6df216e3f571e38d7628b2d7fb6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-dns-faq"></a>Nejčastější dotazy k Azure DNS

## <a name="about-azure-dns"></a>O Azure DNS

### <a name="what-is-azure-dns"></a>Co je Azure DNS?

Systému názvů domény nebo DNS, zodpovídá za překladu (nebo vyřešení) název webu nebo služby na jeho IP adresu. Azure DNS je hostitelská služba domén DNS poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

DNS domén v Azure DNS jsou hostované v Azure globální síti názvových serverů DNS. Tato služba využívá všesměrového vysílání sítě tak, aby každý dotaz DNS je zodpovězen nejbližší server DNS k dispozici. Azure DNS poskytuje vysoký výkon a vysoká dostupnost vaší domény.

Služba Azure DNS je založena na Azure Resource Manager. Jako takový výhody z funkce služby Správce prostředků například řízení přístupu na základě rolí, protokoly auditu a uzamčení prostředků. Doménách a záznamech lze spravovat prostřednictvím portálu Azure, rutin prostředí Azure PowerShell a rozhraní příkazového řádku Azure napříč platformami. Aplikace, které potřebují Automatická správa DNS může integrovat službu pomocí REST API a sady SDK.

### <a name="how-much-does-azure-dns-cost"></a>Kolik Azure DNS stojí?

Model fakturace Azure DNS je založena na počtu zónách DNS hostovaných v Azure DNS a počet dotazů DNS, který obdrží. Slevy jsou poskytovány na základě využití.

Další informace najdete v tématu [Azure DNS stránce s cenami](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaká je smlouva SLA pro Azure DNS?

Azure zaručuje, že platné požadavky DNS obdrží odpověď z alespoň jeden server název Azure DNS alespoň 99,99 % času.

Další informace najdete v tématu [stránku smlouvy SLA pro Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Novinky DNS zóny Je to to samé jako doména DNS? 

Doména je jedinečný název v systému názvů domény, například "contoso.com".

K hostování záznamů DNS v určité doméně se používá zóna DNS. Třeba doména "contoso.com" může obsahovat několik záznamů DNS, třeba "mail.contoso.com" (pro poštovní server) a "www.contoso.com" (pro webový server). Tyto záznamy by být hostovaná v zóně DNS "contoso.com".

Název domény je *pouze název*, zatímco zóny DNS je datový prostředek, který obsahuje záznamy DNS pro název domény. Azure DNS vám umožňuje hostovat zónu DNS a spravovat záznamy DNS pro doménu v Azure. Nabízí taky názvových serverů DNS k odpovědi na dotazy DNS z Internetu.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Když chci používat Azure DNS, musím si kupovat název domény DNS? 

Ne nutně.

K hostování zóny DNS v Azure DNS nemusíte kupovat doménu. Zónu DNS můžete vytvořit kdykoli a nemusíte přitom vlastnit název příslušné domény. Dotazy DNS pro tuto zónu pouze vyřeší, pokud jsou směrované na názvové servery Azure DNS přiřadit do zóny.

Budete muset zakoupit název domény, pokud chcete propojit zóny DNS do globální hierarchie DNS – toto propojení umožňuje dotazy DNS odkudkoli na světě najít zónu DNS a odpověď, v níž svoje záznamy DNS.

## <a name="azure-dns-features"></a>Azure DNS Features

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Podporuje Azure DNS na základě DNS provoz směrování nebo koncový bod převzetí služeb při selhání?

Na základě DNS provoz směrování a koncového bodu převzetí služeb při selhání jsou k dispozici pomocí Azure Traffic Manager. Azure Traffic Manager je samostatný služba Azure, která můžete použít společně s Azure DNS. Další informace najdete v tématu [Traffic Manager Přehled](../traffic-manager/traffic-manager-overview.md).

Azure DNS podporuje pouze hostování "statická" domény DNS, kde každý dotaz DNS pro daný záznam DNS vždycky obdržel stejnou odpověď DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Podporuje Azure DNS registrace názvu domény?

Ne. Azure DNS aktuálně nepodporuje nákup názvů domén. Pokud chcete zakoupit domén, budete muset použít doménového registrátora názvu domény třetí strany. Registrátor obvykle účtuje malý roční poplatek. Pro správu záznamů DNS, může být domény pak hostovaný v Azure DNS. V tématu [delegování domény do Azure DNS](dns-domain-delegation.md) podrobnosti.

Nákup domény je funkce sledován v Azure nevyřízených položek. Můžete použít web pro zasílání názorů na [zaregistrovat podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Podporuje Azure DNS DNSSEC?

Ne. Azure DNS v současné době nepodporuje DNSSEC.

DNSSEC je funkce sledované v Azure DNS nevyřízených položek. Můžete použít web pro zasílání názorů na [zaregistrovat podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Podporuje Azure DNS přenosy zóny (AXFR/IXFR)?

Ne. Azure DNS v současné době nepodporuje přenosy zóny. Zóny DNS může být [importovat do Azure DNS pomocí rozhraní příkazového řádku Azure](dns-import-export.md). Záznamy DNS potom je můžete spravovat prostřednictvím [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), naše [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [rutiny prostředí PowerShell](dns-operations-recordsets.md), nebo [rozhraní příkazového řádku nástroje](dns-operations-recordsets-cli.md).

Přenos zóny je funkce sledované v Azure DNS nevyřízených položek. Můžete použít web pro zasílání názorů na [zaregistrovat podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Podporuje Azure DNS adresy URL přesměrování?

Ne. Adresa URL přesměrování služby nejsou ve skutečnosti služba DNS – pracují na úrovni protokolu HTTP, nikoli na úrovni DNS. Někteří poskytovatelé služby DNS pro adresu URL sady přesměrování služby v rámci jejich celkový nabídky. To není podporováno aktuálně Azure DNS.

Adresa URL přesměrování funkce jsou sledovány v Azure DNS nevyřízených položek. Můžete použít web pro zasílání názorů na [zaregistrovat podporu pro tuto funkci](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Pomocí Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Můžu společné hostování domény pomocí Azure DNS a jiného poskytovatele DNS?

Ano. Azure DNS podporuje společné hosting domén s jinými službami DNS.

To pokud chcete udělat, musíte upravit záznamy NS pro doménu (který ovládací prvek poskytovatelů, kteří obdrží DNS dotazuje pro doménu) tak, aby odkazoval na názvové servery obou zprostředkovatelů. Můžete upravit tyto záznamy NS na třech místech: v Azure DNS, v rámci jiné zprostředkovatele a v nadřazené zóně (obvykle konfiguruje prostřednictvím registrátorem názvu domény). Další informace o delegování DNS najdete v tématu [delegování DNS domény](dns-domain-delegation.md).

Kromě toho budete muset zajistěte, aby záznamy DNS pro doménu synchronizace mezi i poskytovatelé služby DNS. Azure DNS v současné době nepodporuje přenosy zóny DNS. Záznamy DNS je nutné synchronizovat buď pomocí [portálu pro správu Azure DNS](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [rutiny prostředí PowerShell](dns-operations-recordsets.md), nebo [Rozhraní příkazového řádku nástroje](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Je nutné delegovat na všechny čtyři názvových serverů Azure DNS Moje doména?

Ano. Azure DNS přiřadí každé zóny DNS pro odolnost izolace a vyšší odolnosti čtyři názvové servery. K získání oprávnění pro smlouvy SLA pro Azure DNS, budete muset delegování domény do všechny čtyři názvové servery.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jaká jsou omezení využití pro Azure DNS?

Následující výchozí omezení platí při použití Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Můžete přesunout zóny DNS, mezi skupinami prostředků nebo mezi předplatnými?

Ano. Zóny DNS lze přesouvat mezi skupinami prostředků, nebo mezi předplatnými.

Není žádný vliv na dotazy DNS, při přesouvání zóny DNS. Názvové servery přiřazené pro zónu zůstávají stejné a dotazy DNS jsou zpracovány jako normální v průběhu.

Další informace a pokyny, jak přesunout zóny DNS, najdete v části [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak dlouho trvá DNS změny se projeví?

Nové zóny DNS a záznamy DNS jsou obvykle projeví ve názvových serverů Azure DNS rychle během několika sekund.

Změny na existující záznamy DNS může trvat delší dobu, ale měli stále projeví na názvových serverů Azure DNS v rámci 60 sekund. V takovém případě DNS ukládání do mezipaměti mimo Azure DNS (podle rekurzivní překladače služby DNS a klienty DNS) mohou ovlivnit čas potřebný pro změnu DNS účinné. Tato doba trvání mezipaměti se dá řídit pomocí vlastnost Time To Live (TTL) každá sada záznamů.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak lze chránit Moje zóny DNS před náhodným odstraněním?

Azure DNS je spravován pomocí Azure Resource Manager a výhody z řízení přístupu funkce správce Azure Resource Manager poskytuje. Řízení přístupu na základě rolí lze použít k řízení uživatelů, kteří mají čtení nebo zápis do zóny DNS a sady záznamů. Uzamčení prostředků můžete použít sady záznamů a zabránit náhodnému úpravy nebo odstranění zóny DNS.

Další informace najdete v tématu [Protecting zóny DNS a záznamy](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak nastavím záznamy SPF v Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Vyřešit Azure DNS Nameservers přes protokol IPv6? 

Ano. Azure DNS Nameservers jsou duální sada protokolů (mít oba protokoly IPv4 a IPv6 adresy). Pokud chcete zjistit adresu IPv6 pro nameservers Azure DNS přiřazené pro vaší zónu DNS, můžete použít nástroj, jako je například nslookup (například `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Jak lze nastavit až mezinárodní IDN název domény () v Azure DNS?

Mezinárodní názvy domén (IDN) fungovat podle kódování, každý využívající název DNS se[punycode](https://en.wikipedia.org/wiki/Punycode)'. Dotazy DNS jsou vytvářeny pomocí názvy těchto kódováním punycode.

Můžete nakonfigurovat mezinárodní názvy domén (IDN) v Azure DNS první převádění název zóny, nebo název sady záznamu na kódování punycode. Azure DNS v současné době nepodporuje integrovanou převod z punycode.

## <a name="private-dns"></a>Privátní DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Podporuje Azure DNS "privátní" domény?
Podpora domén "privátní" je implementovaná pomocí funkce privátní zóny.  Tato funkce je aktuálně k dispozici ve verzi public preview.  Privátní zóny se spravují pomocí stejné nástroje jako internetového Azure DNS zóny, ale jsou pouze přeložit z v rámci zadaného virtuálních sítí.  Najdete v článku [přehled](private-dns-overview.md) podrobnosti.

V tuto chvíli privátní nejsou podporovány na portálu Azure. 

Informace o dalších interní DNS v Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Jaký je rozdíl mezi registrace virtuální sítě a řešení virtuální sítě v rámci privátní zóny? 
Privátní zóny DNS dvěma způsoby – jako registrace virtuální sítě nebo jako virtuální síť řešení můžete propojit virtuální sítě. V obou případech bude moct úspěšně vyřešit proti záznamů v zóně privátní virtuální počítače ve virtuální síti. Pokud však jako virtuální síť registrace zadat virtuální síť, Azure bude automaticky zaregistrovat záznamy DNS (dynamickou registraci) do zóny pro virtuální počítače ve virtuální síti. Kromě toho když virtuální počítač v registrace virtuální sítě se odstranila, Azure také automaticky odebere odpovídající záznam DNS z propojené privátní zóny. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Bude zón DNS privátní Azure fungovat na všech oblastech Azure?
Ano. Privátní zón je podporována pro překlad DNS mezi virtuálními sítěmi přes Azure oblasti i bez explicitně partnerský vztah virtuální sítě, dokud virtuální sítě jsou zadané jako řešení pro zónu privátní virtuální sítě. Zákazníci mohou potřebovat virtuální sítě budou-li být peered provozu TCP nebo HTTP, které mají být předány z jedné oblasti. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Z virtuální sítě požadované pro privátní zóny je připojení k Internetu?
Ne. Privátní zón fungovat ve spojení s virtuálními sítěmi a Informujte zákazníky, správě domén pro virtuální počítače nebo jiným prostředkům v rámci a virtuální sítí. Je vyžadována pro překlad názvů bez připojení k Internetu. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Dá se stejné zóny privátní použít pro více virtuálních sítí pro řešení? 
Ano. Zákazníci můžete přidružit jeden privátní zóny až 10 řešení virtuálních sítí.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Virtuální síť, která patří do jiného předplatného přidáním jako řešení virtuální sítě na zónu privátní? 
Ano, tak dlouho, dokud uživatel má oprávnění k zápisu operace na virtuálním sítím jak zónu DNS soukromé. Všimněte si, že k více rolím RBAC může být přiděleno oprávnění k zápisu. Například role RBAC Přispěvatel Classic sítě má oprávnění k zápisu do virtuální sítě. Další informace o role RBAC najdete v tématu [řízení přístupu na základě rolí](../active-directory/role-based-access-control-what-is.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Záznamy DNS automaticky registrované virtuálního počítače v zóně s privátní se automaticky odstraní když virtuální počítače jsou odstraněny zákazník?
Ano. Při odstranění virtuálního počítače v rámci virtuální sítě registrace jsme se automaticky odstraní záznamy DNS, které byly zapsány do zóny kvůli Probíhá registrace virtuální sítě. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Můžete záznam automaticky registrované virtuálního počítače v zóně s privátní (z virtuální sítě registrace) ručně odstranit? 
Ne. V tomto okamžiku nejsou záznamy DNS virtuálního počítače, které se automaticky registruje v privátní zóny z virtuální sítě registrace viditelný nebo upravovat zákazníků. Ale můžete nahradit (Přepsat) takové automaticky registrované záznamy DNS ručně vytvořené DNS záznamů v zóně. Viz následující otázku a odpověď, která řeší to.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Co se stane, když jsme pokusí ručně vytvořit nový záznam DNS do zóny privátní, která má stejný název hostitele jako (automaticky registrované) existujícího virtuálního počítače ve virtuální síti registraci? 
Pokud se pokusíte ručně vytvořit nový záznam DNS do zóny privátní, která má stejný název hostitele jako existující virtuální počítač (automaticky registrované) ve virtuální síti registrace, jsme vám umožní přepsat automaticky zaregistrovanou nový záznam DNS virtuální počítač záznam. Kromě toho pokud následně pokusíte odstranit tento ručně vytvořit záznam DNS ze zóny, bude úspěšné odstranění a Automatická registrace dojde znovu (záznam DNS bude znovu vytvořena automaticky v zóně) dlouho jako virtuální počítač stále existuje a má privátní IP Adresou k němu připojen. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Co se stane, když jsme zrušit propojení virtuální sítě registrace umožňuje ze zóny privátní? By záznamy automaticky registrované virtuálního počítače z virtuální sítě se odebere ze zóny také?
Ano. Pokud zrušíte ze zóny privátní propojení virtuální sítě registrace (aktualizaci zóny DNS odebrat přidružené registrace virtuální sítě), bude Azure odebrat všechny záznamy automaticky registrované virtuálního počítače ze zóny. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Co se stane, když jsme registrační (nebo řešení) virtuální síť, která je propojený na zónu privátní odstranit? Budeme muset ručně aktualizovat privátní zóny zrušení-propojení virtuální sítě virtuální síti jako registrace (nebo řešení) ze zóny?
Ano. Při odstranění registrace (nebo řešení) virtuální sítě bez oddělení od privátního zónu nejprve, Azure vám umožní operaci odstranění úspěšné, ale virtuální sítě není automaticky odpojil od privátní zóny případných. Budete muset ručně zrušit propojení virtuální sítě z privátní zóny. Z tohoto důvodu doporučujeme nejprve zrušit propojení virtuální sítě z vaší privátní zóně před odstraněním.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>By překlad DNS pomocí výchozího plně kvalifikovaný název domény (internal.cloudapp.net) i nadále fungovat, i když zóna privátní (například: contoso.local) je propojena k virtuální síti? 
Ano. Funkce privátní zón nenahrazuje rozlišení DNS výchozí, pomocí Azure internal.cloudapp.net zóny a je nabízena jako další funkce nebo vylepšení. U obou případech (jestli přijímající na internal.cloudapp.net Azure nebo na vlastní privátní zóny) se doporučuje použít plně kvalifikovaný název domény chcete vyřešit proti zóny. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>By byla změněna příponu DNS na virtuální počítače v rámci virtuální sítě propojené u pásma privátní? 
Ne. V tomto okamžiku příponu DNS na virtuální počítače ve vaší virtuální sítě propojené zůstanou jako výchozí příponu Azure ("*. internal.cloudapp.net"). Tato přípona DNS na virtuálních počítačích můžete ale ručně změnit u privátní pásma. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Existují nějaká omezení pro privátní zóny během této verze preview?
Ano. Chcete-li verzi Public Preview existují tato omezení:
* 1 virtuální sítě registrace za soukromé zóny
* Až 10 řešení virtuální sítě na privátní zóny
* Dané virtuální síti lze propojit jen na jednu zónu privátní jako virtuální síť registrace
* Dané virtuální síti může být propojený až 10 privátní zóny jako řešení virtuální sítě
* Pokud je zadán virtuální síť registrace, záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k zóně privátní nebude viditelná a získat z prostředí Powershell nebo rozhraní příkazového řádku nebo rozhraní API, ale záznamy virtuálních počítačů jsou skutečně zaregistrované a vyřešit úspěšně
* Zpětné DNS bude fungovat pouze pro adresní prostor privátní IP ve virtuální síti registrace
* Reverse DNS pro privátní IP adresu, která není registrována v zóně privátní (například: privátní IP pro virtuální počítač ve virtuální síti, který bude propojen jako řešení virtuální sítě na zónu privátní) vrátí "internal.cloudapp.net" jako přípona DNS, ale Tato přípona nebude možné přeložit.   
* Virtuální síť musí být prázdná (tj. žádné virtuální počítače s síťový adaptér připojený) při počátečním (tj. prvním) propojení na zónu privátní jako registraci nebo řešení virtuální síť. Virtuální síť pak lze však není prázdná pro budoucí propojování jako virtuální síť registraci nebo řešení, k jiné privátní zóny. 
* V tomto okamžiku podmíněné předávání není podporován, například pro povolení rozlišení mezi Azure a místní sítě. Dokumentace na tom, jak zákazníci můžou realizovat tento scénář prostřednictvím jiným mechanismem, najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Jsou některé kvóty nebo omezení pro zóny nebo záznamy pro privátní zóny?
Neexistují žádné samostatné omezení na počet zón povoleno jedno předplatné, nebo počet sad záznamů na zóny pro privátní zóny. Veřejné a privátní zón započítává k celkové omezení DNS, jak je uvedeno [sem](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Je k dispozici podpora portál pro privátní zóny?
Privátní zón, které jsou už vytvořené pomocí jiných portál mechanismy (rozhraní API nebo PowerShell nebo rozhraní příkazového řádku/SDK) bude zobrazovat na portálu Azure, ale zákazníků se nepodařilo se vytvořit nový privátní zóny nebo spravovat přidružení s virtuálními sítěmi. Kromě toho pro virtuální sítě přidružený jako registrace virtuálních sítí, automaticky registrované záznamy virtuálního počítače nebudou viditelná z portálu. 

## <a name="next-steps"></a>Další postup

[Další informace o službě Azure DNS](dns-overview.md)
<br>
[Další informace o používání Azure DNS pro privátní domény](private-dns-overview.md)
<br>
[Další informace o zóny DNS a záznamů](dns-zones-records.md)
<br>
[Začínáme s Azure DNS](dns-getstarted-portal.md)

