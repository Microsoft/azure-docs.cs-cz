---
title: Nejčastější dotazy k Azure Firewall
description: Nejčastější dotazy týkající se Azure Firewall. Spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/29/2020
ms.author: victorh
ms.openlocfilehash: 78269461bf01d61bffeed504b0168b4913c6e131
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77442985"
---
# <a name="azure-firewall-faq"></a>Nejčastější dotazy k Azure Firewall

## <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jaké funkce jsou podporovány v Azure Firewall?

* Stavová brána firewall jako služba
* Integrovaná vysoká dostupnost s neomezenou škálovatelností cloudu
* Filtrování FQDN
* Značky plně kvalifikovaných názvů domén
* Pravidla filtrování síťového provozu
* Podpora pro odchozí SNAT
* Podpora DNAT u příchozích přenosů
* Centrální vytváření, prosazování a protokolování zásad pro připojení aplikací a sítí napříč předplatnými Azure a virtuální sítě
* Plně integrovaná s Azure Monitor pro protokolování a analýzu

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Jaký je typický model nasazení pro Azure Firewall?

Můžete nasadit Azure Firewall v jakékoli virtuální síti, ale zákazníci ji obvykle nasadí do centrální virtuální sítě a ostatní virtuální sítě na ni v modelu hvězdicové sítě. Pak můžete nastavit výchozí trasu z partnerských virtuálních sítí tak, aby odkazovaly na tuto virtuální síť centrální brány firewall. Globální partnerský vztah virtuálních sítí se podporuje, ale nedoporučuje se z důvodu potenciálních problémů s výkonem a latencí v různých oblastech. Pro dosažení nejlepšího výkonu nasaďte jednu bránu firewall na oblast.

Výhodou tohoto modelu je schopnost centrálně ovládat více paprskových virtuální sítě napříč různými předplatnými. K dispozici jsou také úspory nákladů, protože nemusíte v každé virtuální síti samostatně nasazovat bránu firewall. Úspory nákladů by se měly měřit v závislosti na vzorech provozu zákazníků.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak můžu nainstalovat Azure Firewall?

Azure Firewall můžete nastavit pomocí Azure Portal, PowerShellu, REST API nebo pomocí šablon. Podrobné pokyny najdete v tématu [kurz: nasazení a konfigurace Azure firewall pomocí Azure Portal](tutorial-firewall-deploy-portal.md) .

## <a name="what-are-some-azure-firewall-concepts"></a>Co jsou některé Azure Firewall koncepty?

Azure Firewall podporuje kolekce pravidel a pravidel. Kolekce pravidel je sada pravidel, která sdílí stejné pořadí a prioritu. Kolekce pravidel jsou spouštěny v pořadí podle priority. Kolekce pravidel sítě mají vyšší prioritu než kolekce pravidel aplikací a všechna pravidla se ukončí.

Existují tři typy kolekcí pravidel:

* *Pravidla aplikací*: Nakonfigurujte plně kvalifikované názvy domén (FQDN), ke kterým se dá dostat z podsítě.
* *Síťová pravidla*: Nakonfigurujte pravidla, která obsahují zdrojové adresy, protokoly, cílové porty a cílové adresy.
* *Pravidla překladu adres (NAT)* : NAKONFIGURUJTE pravidla DNAT tak, aby povolovala příchozí připojení.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Podporuje Azure Firewall filtrování příchozích přenosů?

Azure Firewall podporuje filtrování příchozího a odchozího přenosu. Příchozí ochrana se obvykle používá pro protokoly jiné než HTTP/S. Například protokoly RDP, SSH a FTP. Pro nejlepší příchozí ochranu HTTP/S použijte Firewall webových aplikací, jako je třeba [Firewall webových aplikací Azure v azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Které služby protokolování a analýzy podporuje Azure Firewall?

Azure Firewall je integrována s Azure Monitor pro zobrazení a analýzu protokolů brány firewall. Protokoly lze odeslat do Log Analytics, Azure Storage nebo Event Hubs. Je možné je analyzovat v Log Analytics nebo různými nástroji, jako je Excel a Power BI. Další informace najdete v tématu [kurz: monitorování protokolů Azure firewall](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak Azure Firewall funguje jinak než u stávajících služeb, jako je síťová virtuální zařízení na webu Marketplace?

Azure Firewall je základní služba brány firewall, která dokáže řešit určité scénáře zákazníků. Očekává se, že budete mít kombinaci síťová virtuální zařízení a Azure Firewall třetích stran. Lepší spolupráce je základní prioritou.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaký je rozdíl mezi Application Gateway WAF a Azure Firewall?

Firewall webových aplikací (WAF) je funkce Application Gateway, která poskytuje centralizovanou příchozí ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Azure Firewall poskytuje příchozí ochranu pro protokoly jiné než HTTP/S (například RDP, SSH, FTP), odchozí ochranu na úrovni sítě pro všechny porty a protokoly a ochranu na úrovni aplikace pro odchozí HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaký je rozdíl mezi skupinami zabezpečení sítě (skupin zabezpečení sítě) a Azure Firewall?

Služba Azure Firewall doplňuje funkce skupiny zabezpečení sítě. Společně poskytují lepší "zabezpečení sítě". Skupiny zabezpečení sítě poskytují distribuované filtrování přenosů síťové vrstvy, které umožňuje omezit provoz na prostředky v rámci virtuálních sítí v každém předplatném. Azure Firewall je plně stavová, centralizovaná síťová brána firewall jako služba, která zajišťuje ochranu na úrovni sítě a aplikace napříč různými předplatnými a virtuálními sítěmi.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Jsou v Azure Firewall podsíti podporovány skupiny zabezpečení sítě (skupin zabezpečení sítě)?

Azure Firewall je spravovaná služba s více vrstvami ochrany, včetně ochrany platforem s úrovní síťového rozhraní skupin zabezpečení sítě (nelze zobrazit).  V Azure Firewall podsíti není vyžadována úroveň podsítě skupin zabezpečení sítě a jsou zakázané, aby nedošlo k přerušení služeb.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Návody nastavit Azure Firewall pomocí koncových bodů služby?

Pro zabezpečený přístup ke službám PaaS doporučujeme koncovým bodům služby. Můžete se rozhodnout, že v podsíti Azure Firewall povolíte koncové body služby a zakážete je ve virtuálních sítích s propojenými paprsky. Tímto způsobem můžete využít výhod obou funkcí – zabezpečení koncového bodu služby a centrální protokolování pro veškerý provoz.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jaké jsou ceny za Azure Firewall?

Viz [ceny Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak můžu zastavit a spustit Azure Firewall?

Můžete použít metody *navrácení* a *přidělení* Azure PowerShell.

Například:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Pro původní skupinu prostředků a předplatné musíte znovu přidělit bránu firewall a veřejnou IP adresu.

## <a name="what-are-the-known-service-limits"></a>Jaké jsou známá omezení služby?

Omezení služby Azure Firewall najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Může Azure Firewall ve virtuální síti rozbočovače dopřed a filtrovat síťový provoz mezi dvěma virtuálními sítěmi?

Ano, Azure Firewall můžete ve virtuální síti rozbočovače použít k směrování a filtrování provozu mezi dvěma koncovými virtuálními sítěmi. Podsítě v každé virtuální síti s paprsky musí mít UDR, která odkazuje na Azure Firewall jako výchozí bránu, aby tento scénář správně fungoval.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Může Azure Firewall dopředný a filtrovaný síťový provoz mezi podsítěmi ve stejné virtuální síti nebo v partnerských virtuálních sítích?

Ano. Konfigurace udr pro přesměrování provozu mezi podsítěmi ve stejné virtuální síti ale vyžaduje další pozornost. Při použití rozsahu adres virtuální sítě jako cílové předpony pro UDR je to také směrování všech přenosů z jednoho počítače do jiného počítače ve stejné podsíti prostřednictvím instance Azure Firewall. Zabráníte tak, že zadáte trasu pro podsíť v UDR s typem dalšího segmentu směrování **VNet**. Správa těchto tras může být nenáročných a náchylná k chybě. Doporučenou metodou pro interní segmentaci sítě je použití skupin zabezpečení sítě, které nevyžadují udr.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Je Azure Firewall odchozí SNAT mezi privátními sítěmi?

Azure Firewall nesnat, pokud je cílová IP adresa rozsahem privátních IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall SNATs provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Azure Firewall **můžete nakonfigurovat tak,** aby nesnat na svůj rozsah veřejných IP adres. Další informace najdete v tématu [Azure firewall rozsahy privátních IP adres SNAT](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Je podporováno vynucené tunelové propojení/řetězení k síťovému virtuálnímu zařízení?

Vynucené tunelování není aktuálně podporováno. Azure Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte tuto hodnotu přepsat hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro udržování přímého připojení k Internetu.

Pokud vaše konfigurace vyžaduje vynucené tunelování v místní síti a můžete určit předpony cílových IP adres pro vaše internetové cíle, můžete tyto rozsahy nakonfigurovat v místní síti jako další segment směrování prostřednictvím uživatelsky definované trasy. AzureFirewallSubnet. Nebo můžete k definování těchto tras použít protokol BGP.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Existují nějaká omezení skupiny prostředků brány firewall?

Ano. Brána firewall, podsíť, virtuální síť a veřejná IP adresa musí být ve stejné skupině prostředků.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Když konfigurujete DNAT pro příchozí síťový provoz, musím taky nakonfigurovat odpovídající síťové pravidlo, které povolí tento provoz?

Ne. Pravidla překladu adres (NAT) implicitně přidávají odpovídající síťové pravidlo, které povoluje přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak zástupné znaky fungují v cílovém plně kvalifikovaném názvu domény pravidla aplikace?

Pokud nakonfigurujete * **. contoso.com**, povolí *anyvalue*. contoso.com, ale ne contoso.com (vrchol domény). Pokud chcete povolený vrchol domény, musíte ho explicitně nakonfigurovat jako cílový plně kvalifikovaný název domény.

## <a name="what-does-provisioning-state-failed-mean"></a>Co znamená *stav zřizování: neúspěšné* ?

Pokaždé, když se použije Změna konfigurace, Azure Firewall se pokusí aktualizovat všechny své základní back-end instance. Ve výjimečných případech se může stát, že jedna z těchto back-end instancí selže při aktualizaci s novou konfigurací a proces aktualizace se zastaví se stavem zřizování. Vaše Azure Firewall je stále v provozu, ale použitá konfigurace může být v nekonzistentním stavu, kde některé instance mají předchozí konfiguraci, kde ostatní mají aktualizovanou sadu pravidel. Pokud k tomu dojde, zkuste aktualizovat konfiguraci ještě jednou, dokud se operace nezdaří a vaše brána firewall je v *úspěšném* stavu zřizování.

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Jak Azure Firewall zvládnout plánované údržby a neplánovaných selhání?
Azure Firewall se skládá z několika back-end uzlů v konfiguraci aktivní-aktivní.  U jakékoli plánované údržby máme připojení k dispozici pro bezproblémové aktualizace uzlů.  Aktualizace se plánují během nepracovních hodin pro každou oblast Azure, abyste mohli dále omezit riziko přerušení.  Pro neplánované problémy vytvoříme instanci nového uzlu, který nahradí uzel, který selhal.  Připojení k novému uzlu se obvykle znovu vytvoří během 10 sekund od doby selhání.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Je pro název brány firewall nějaký limit znaků?

Ano. Pro název brány firewall je povolený limit 50 znaků.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Proč Azure Firewall potřebovat velikost podsítě/26?

Azure Firewall musí zřídit při škálování víc instancí virtuálních počítačů. A/26 adresního prostoru zajišťuje, že brána firewall má k dispozici dostatek IP adres pro přizpůsobení škálování.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Je potřeba změnit velikost podsítě brány firewall při škálování služby?

Ne. Azure Firewall nepotřebuje podsíť větší než/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Jak můžu zvýšit propustnost brány firewall?

Počáteční kapacita propustnosti Azure Firewall je 2,5 – 3 GB/s. V současné době je horizontální navýšení kapacity založené jenom na využití procesoru. V některých případech brána firewall se síťovými pravidly neumožňuje škálovat až do zvýšení propustnosti, protože Síťová pravidla nemají výrazný vliv na využití procesoru. Pokud potřebujete vyšší propustnost pro bránu firewall, obraťte se na podporu, aby se zvýšila kapacita počáteční propustnosti brány firewall.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Jak dlouho trvá Azure Firewall k horizontálnímu navýšení kapacity?

V současné době trvá Azure Firewall horizontálního navýšení kapacity od pěti do sedmi minut. Pokud máte shluky, které vyžadují rychlejší automatické škálování, obraťte se na podporu, aby se zvýšila kapacita počáteční propustnosti brány firewall.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Má Azure Firewall ve výchozím nastavení povolený přístup ke službě Active Directory?

Ne. Azure Firewall blokuje přístup ke službě Active Directory ve výchozím nastavení. Pokud chcete přístup udělit, nakonfigurujte značku služby Azureactivedirectory selhala. Další informace najdete v tématu [Azure firewall značky služby](service-tags.md).
