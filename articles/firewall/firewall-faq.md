---
title: Nejčastější dotazy k Azure bránu Firewall
description: Nejčastější dotazy k Azure bránu Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 5/3/2019
ms.author: victorh
ms.openlocfilehash: 4c4a6776e3bb56026a48963ec83fe582380c68d0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145959"
---
# <a name="azure-firewall-faq"></a>Nejčastější dotazy k Azure bránu Firewall

## <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavové brány firewall na-jako službu s integrovanou vysokou dostupnost a škálovatelnost cloudu neomezený. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jaké funkce jsou podporovány v bráně Firewall Azure?

* Stavová brána firewall jako služba
* Integrovaná vysoká dostupnost s neomezenou škálovatelností cloudu
* Filtrování FQDN
* Značky plně kvalifikovaných názvů domén
* Pravidla filtrování síťového provozu
* Podpora pro odchozí SNAT
* Podpora DNAT u příchozích přenosů
* Centrálně vytvoření, vynucení a protokolovat zásady aplikace a síťové připojení mezi virtuálními sítěmi a předplatných Azure
* Plná integrace se službou Azure Monitor zajišťující protokolování a analýzy

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co je typické nasazení modelu pro Brána Firewall služby Azure?

Brána Firewall služby Azure můžete nasadit na všechny virtuální sítě, ale zákazníci obvykle nasazení v centrální virtuální síti a vytvořit partnerský vztah jiným virtuálním sítím v modelu střed a paprsek do něj. Pak můžete nastavit výchozí trasu v partnerských virtuálních sítích tak, aby odkazoval na tento centrální brány firewall virtuální sítě. Globální VNet peering se podporuje, ale nedoporučuje kvůli potenciální výkon a problémy s latencí napříč oblastmi. Pro zajištění nejlepšího výkonu nasaďte jednu bránu firewall v jedné oblasti.

Výhodou tohoto modelu je schopnost centrálně získat ovládací prvek na více virtuálních sítí paprsků napříč různými předplatnými. Je také úspory nákladů, které není potřeba brána firewall v každé virtuální síti nasadit samostatně. Úspory nákladů, které by se mělo měřit a přidružit partnerského vztahu náklady na základě vzorců provozu zákazníka.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak nainstalovat Azure bránu Firewall?

Brána Firewall služby Azure můžete nastavit pomocí webu Azure portal, Powershellu, rozhraní REST API nebo pomocí šablon. Zobrazit [kurzu: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md) podrobné pokyny.

## <a name="what-are-some-azure-firewall-concepts"></a>Jaké jsou některé pojmy Brána Firewall služby Azure?

Azure Brána Firewall podporuje pravidel a kolekcí pravidel. Kolekce pravidel je sada pravidel, která mají stejné pořadí a priority. Kolekce pravidel jsou provedeny v pořadí podle jejich priority. Kolekce pravidel sítě mají vyšší prioritu než kolekcí pravidel aplikaci a všechna pravidla se ukončuje.

Existují tři typy kolekcí pravidel:

* *Pravidla aplikací*: Konfigurace plně kvalifikované názvy domény (FQDN), které mohou být přístupné z podsítě.
* *Pravidla síťových*: Konfigurace pravidel, která obsahují zdrojové adresy, protokoly, cílové porty a cílové adresy.
* *Pravidla NAT*: Konfigurace pravidel DNAT povolovat příchozí připojení.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Brána Firewall služby Azure podporuje filtrování příchozího provozu?

Azure Brána Firewall podporuje filtrování příchozího a odchozího. Příchozí ochrany je pro protokoly než HTTP/S. Například RDP, SSH a FTP protokoly.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Které protokolování a analýza služby jsou podporovány bránou Azure Firewall?

Brány Firewall na Azure je integrovaná se službou Azure Monitor pro zobrazení a analýza protokolů brány firewall. Protokoly můžete odeslat do Log Analytics, Azure Storage nebo služby Event Hubs. Mohou být analyzovány v Log Analytics, nebo prostřednictvím různých nástrojů, jako je Excel a Power BI. Další informace najdete v tématu [kurzu: Monitorujte protokoly brány Firewall Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak se Brána Firewall služby Azure z existujících služeb, jako je například síťová virtuální zařízení na webu Marketplace funguje jinak?

Brány Firewall na Azure je služba základní brána firewall, která může vyřešit určitých scénářů zákazníků. Očekává se, zda bude mít kombinaci síťových virtuálních zařízení a Brána Firewall služby Azure třetích stran. Lepší spolupráci se základní prioritou.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaký je rozdíl mezi waf služby Application Gateway a Brána Firewall služby Azure?

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu příchozí webových aplikací před běžným zneužitím a ohrožení zabezpečení. Brána Firewall Azure poskytuje příchozí ochranu pro protokoly než HTTP/S (například protokol RDP, SSH, FTP), odchozí ochrany na úrovni sítě pro všechny porty a protokoly a ochrany na úrovni aplikace pro odchozí HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaký je rozdíl mezi skupiny zabezpečení sítě (Nsg) a Brána Firewall služby Azure?

Brána Firewall služby Azure service doplňuje funkci skupiny zabezpečení sítě. Společně poskytují lepší zabezpečení sítě "v obrany". Skupiny zabezpečení sítě poskytují distribuované síťový provoz vrstvy filtrování pro omezení provozu směřujícího do prostředků v rámci virtuálních sítí v každém předplatném. Brány Firewall na Azure je plně stavové a centralizované síťové brány firewall jako služby, která poskytuje ochranu na úrovni sítě a aplikace v rámci různých předplatných a virtuální sítě.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak nastavit Brána Firewall služby Azure se Moje koncové body služby?

Zabezpečený přístup ke službám PaaS doporučujeme koncových bodů služby. Můžete povolit koncové body služby v podsíti brány Firewall na Azure a zakázat ve virtuálních sítích propojených paprsku. Tímto způsobem, můžete využívat funkce – zabezpečení koncového bodu služby a centrálního protokolování pro veškerý provoz.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jaké jsou ceny za Firewall služby Azure?

Zobrazit [ceny Azure bránu Firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak můžete zastavit a spustit Brána Firewall služby Azure?

Můžete použít Azure PowerShell *uvolnit* a *přidělit* metody.

Příklad:

```azurepowershell
# Stop an exisitng firewall

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
> Brány firewall a veřejná IP adresa musí znovu přidělte do původní skupiny prostředků a předplatném.

## <a name="what-are-the-known-service-limits"></a>Jaká jsou omezení služeb?

Omezení služby Brána Firewall služby Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Brána Firewall služby Azure můžete ve virtuální síti centra vpřed a filtrování síťového provozu mezi dvěma virtuálními sítěmi paprsků?

Ano, brána Firewall služby Azure můžete v centrální virtuální síti pro směrování a filtrování provozu mezi dvěma paprsku virtuální sítě. Podsítě v každé virtuální sítě paprsků, musíte mít UDR přejdete Brána Firewall služby Azure jako výchozí brána pro tento scénář tak, aby správně fungovat.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Brána Firewall služby Azure můžete předat dál a filtrování síťového provozu mezi podsítěmi ve stejné virtuální síti nebo v partnerských virtuálních sítích?

Ano. Konfigurace trasy definované uživatelem pro přesměrování přenosu dat mezi podsítěmi ve stejné virtuální síti, ale vyžaduje další pozornost. Při používání rozsah adres virtuální sítě je dostatečná předponu cíl pro uživatelem definovaná TRASA, to také směruje veškerý provoz z jednoho počítače do jiného počítače ve stejné podsíti prostřednictvím instance Brána Firewall služby Azure. Abyste tomu předešli, obsahoval trasu pro podsíť v uživatelem definovaná TRASA s typem dalšího segmentu směrování z **VNET**. Správa tyto trasy může být náročné a náchylné k chybám. Doporučenou metodou pro interní síť segmentace, je použití skupin zabezpečení sítě, které nevyžadují trasy definované uživatelem.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Musí tunelové propojení/řetězení síťové virtuální zařízení podporované?

Ve výchozím nastavení vynucené tunelování se nepodporuje, ale lze je aktivovat pomocí pomoc od podpory.

Azure brány Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet učí výchozí trasu k vaší místní síti přes protokol BGP, je nutné to přepsat s UDR 0.0.0.0/0 s **NextHopType** hodnota nastavená na **Internet** udržovat s přímým přístupem Připojení k Internetu. Ve výchozím nastavení brána Firewall služby Azure nepodporuje vynuceného tunelování k místní síti.

Však vyžaduje-li vaše konfigurace vynuceného tunelování k místní síti, Microsoft bude podporovat v případ od případu. Takže si můžete přečíst váš případ, obraťte se na podporu. Pokud přijat, vytvoříme seznamu povolených IP adres vaše předplatné a ujistěte se, že se zachová připojení k Internetu vyžaduje bránu firewall.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Existují všechny brány firewall omezení skupin prostředků?

Ano. Brána firewall, podsítě, virtuální sítě a veřejné IP adresy musí být ve stejné skupině prostředků.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Při konfiguraci DNAT pro příchozí síťový provoz, také potřeba nakonfigurovat odpovídající pravidlo sítě, které umožňují tento přenos dat?

Ne. Pravidla NAT implicitně přidat odpovídající pravidlo sítě přeložené provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak fungují zástupné znaky v cíli pravidla aplikace plně kvalifikovaný název domény?

Pokud nakonfigurujete ***. contoso.com**, umožňuje *anyvalue*. contoso.com, ale ne contoso.com (vrcholu domény). Pokud chcete povolit vrcholu domény, musíte ho explicitně nakonfigurovat jako cílový plně kvalifikovaný název domény.

## <a name="what-does-provisioning-state-failed-mean"></a>Co dělá *Stav zřizování: Nepovedlo* znamenají?

Pokaždé, když se zavádí změna konfigurace, brána Firewall služby Azure se pokusí aktualizovat všechny její podkladové instance back-endu. Ve výjimečných případech některé z těchto instancí back-end může selhat aktualizovat s novou konfigurací a proces aktualizace se zastaví s stavu selhání zřizování. Je vaše Brána Firewall služby Azure stále v provozu, ale použitá konfigurace může být v nekonzistentním stavu, kdy některé instance mají předchozí konfiguraci, kde ho mají aktualizované sady pravidel. Pokud k tomu dojde, zkuste aktualizovat konfiguraci ještě jednou až úspěšné operaci se vaše Brána Firewall *Succeeded* Stav zřizování.
