---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 10/15/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1d52c9b3b9b91873bd6f34443f162c74d724a65c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49367132"
---
### <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavové brány firewall na-jako službu s integrovanou vysokou dostupnost a škálovatelnost cloudu neomezený. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jaké funkce jsou podporovány v bráně Firewall Azure?

* Stavová brána firewall jako služba
* Integrovaná vysoká dostupnost s neomezenou škálovatelností cloudu
* Filtrování FQDN
* Značky plně kvalifikovaných názvů domén
* Pravidla filtrování síťového provozu
* Podpora pro odchozí SNAT
* Podpora DNAT u příchozích přenosů
* Centrálně vytvoření, vynucení a protokolovat zásady aplikace a síťové připojení mezi virtuálními sítěmi a předplatných Azure
* Plná integrace se službou Azure Monitor zajišťující protokolování a analýzy

### <a name="what-is-the-pricing-for-azure-firewall"></a>Jaké jsou ceny za Firewall služby Azure?

Azure Brána Firewall nemá hradit fixní částku + náklady na proměnnou:

* Fixní poplatek: $1.25/firewall/hour
* Proměnlivá částka: $0.03/ GB zpracovaných brány firewall (příchozí nebo odchozí)

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co je typické nasazení modelu pro Brána Firewall služby Azure?

Brána Firewall služby Azure můžete nasadit na všechny virtuální sítě, ale zákazníci obvykle nasazení v centrální virtuální síti a vytvořit partnerský vztah jiným virtuálním sítím v modelu střed a paprsek do něj. Pak můžete nastavit výchozí trasu v partnerských virtuálních sítích tak, aby odkazoval na tento centrální brány firewall virtuální sítě.

### <a name="how-can-i-install-the-azure-firewall"></a>Jak nainstalovat Azure bránu Firewall?

Brána Firewall služby Azure můžete nastavit pomocí webu Azure portal, Powershellu, rozhraní REST API nebo pomocí šablon. Zobrazit [kurz: nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) podrobné pokyny.

### <a name="what-are-some-azure-firewall-concepts"></a>Jaké jsou některé pojmy Brána Firewall služby Azure?

Azure Brána Firewall podporuje pravidel a kolekcí pravidel. Kolekce pravidel je sada pravidel, která mají stejné pořadí a priority. Kolekce pravidel jsou provedeny v pořadí podle jejich priority. Kolekce pravidel sítě mají vyšší prioritu než kolekcí pravidel aplikaci a všechna pravidla se ukončuje.

Existují dva typy kolekcí pravidel:

* *Pravidla aplikací*: umožňují nakonfigurovat plně kvalifikované názvy domény (FQDN), které mohou být přístupné z podsítě.
* *Pravidla síťových*: je možné nakonfigurovat pravidla, která obsahují zdrojové adresy, protokoly, cílové porty a cílové adresy.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Brána Firewall služby Azure podporuje filtrování příchozího provozu?

Azure Brána Firewall podporuje filtrování příchozího a odchozího. Příchozí ochrany je pro protokoly než HTTP/S. Například RDP, SSH a FTP protokoly.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Které protokolování a analýza služby jsou podporovány bránou Azure Firewall?

Brány Firewall na Azure je integrovaná se službou Azure Monitor pro zobrazení a analýza protokolů brány firewall. Protokoly můžete odeslat do Log Analytics, Azure Storage nebo služby Event Hubs. Mohou být analyzovány v Log Analytics, nebo prostřednictvím různých nástrojů, jako je Excel a Power BI. Další informace najdete v tématu [kurz: Brána Firewall služby Azure Monitor protokoly](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak se Brána Firewall služby Azure z existujících služeb, jako je například síťová virtuální zařízení na webu Marketplace funguje jinak?

Brány Firewall na Azure je služba základní brána firewall, která může vyřešit určitých scénářů zákazníků. Očekává se, zda bude mít kombinaci síťových virtuálních zařízení a Brána Firewall služby Azure třetích stran. Lepší spolupráci se základní prioritou.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaký je rozdíl mezi waf služby Application Gateway a Brána Firewall služby Azure?

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu příchozí webových aplikací před běžným zneužitím a ohrožení zabezpečení. Brána Firewall Azure poskytuje příchozí ochranu pro protokoly než HTTP/S (například protokol RDP, SSH, FTP), odchozí ochrany na úrovni sítě pro všechny porty a protokoly a ochrany na úrovni aplikace pro odchozí HTTP/S.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaký je rozdíl mezi skupiny zabezpečení sítě (Nsg) a Brána Firewall služby Azure?

Brána Firewall služby Azure service doplňuje funkci skupiny zabezpečení sítě. Společně poskytují lepší zabezpečení sítě "v obrany". Skupiny zabezpečení sítě poskytují distribuované síťový provoz vrstvy filtrování pro omezení provozu směřujícího do prostředků v rámci virtuálních sítí v každém předplatném. Brány Firewall na Azure je plně stavové a centralizované síťové brány firewall jako služby, která poskytuje ochranu na úrovni sítě a aplikace v rámci různých předplatných a virtuální sítě.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak nastavit Brána Firewall služby Azure se Moje koncové body služby?

Zabezpečený přístup ke službám PaaS doporučujeme koncových bodů služby. Můžete povolit koncové body služby v podsíti brány Firewall na Azure a zakázat ve virtuálních sítích propojených paprsku. Tímto způsobem, můžete využívat funkce – zabezpečení koncového bodu služby a centrálního protokolování pro veškerý provoz.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak můžete zastavit a spustit Brána Firewall služby Azure?

Můžete použít Azure PowerShell *uvolnit* a *přidělit* metody.

Příklad:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Brány firewall a veřejná IP adresa musí znovu přidělte do původní skupiny prostředků a předplatném.

### <a name="what-are-the-known-service-limits"></a>Jaká jsou omezení služeb?

* Azure Brána Firewall nemá doporučeného limitu pro 1000 TB za firewall za měsíc.
* Instance Brána Firewall služby Azure, na kterém běží v centrální virtuální síť má omezení, maximálně 50 virtuálních sítí paprsků partnerský vztah virtuální sítě.  
* Brány Firewall Azure nefunguje s globální partnerský vztah, takže byste měli mít minimálně jedno nasazení brány firewall v jedné oblasti.
* Azure Brána Firewall podporuje 10 tisíc pravidel aplikací a 10 tisíc pravidel sítě.

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Brána Firewall služby Azure můžete ve virtuální síti centra vpřed a filtrování síťového provozu mezi dvěma virtuálními sítěmi paprsků?

Ano, brána Firewall služby Azure můžete v centrální virtuální síti pro směrování a filtrování provozu mezi dvěma paprsku virtuální sítě. Podsítě v každé virtuální sítě paprsků, musíte mít UDR přejdete Brána Firewall služby Azure jako výchozí brána pro tento scénář tak, aby správně fungovat.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>Brána Firewall služby Azure můžete předat dál a filtrování síťového provozu mezi podsítěmi ve stejné virtuální síti?

Provoz mezi podsítěmi ve stejné virtuální síti nebo přímo partnerské virtuální síti je směrován přímo i v případě směrování definovaného uživatelem odkazuje na brány Firewall Azure jako výchozí brána. Doporučenou metodou pro interní síť segmentace, je pomocí skupin zabezpečení sítě. K odeslání podsítě pro podsíť provozu do brány firewall v tomto scénáři, musí obsahovat UDR předpona cílové podsítě sítě explicitně v obou podsítích.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Existují všechny brány firewall omezení skupin prostředků?

Ano. Brána firewall, podsítě, virtuální sítě a veřejné IP adresy musí být ve stejné skupině prostředků.
