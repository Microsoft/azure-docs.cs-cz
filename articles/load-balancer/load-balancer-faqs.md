---
title: Nejčastější dotazy – Azure Load Balancer
description: Odpovědi na nejčastější dotazy týkající se Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 2e559d574413b8eb0be2303798e0b16bfffad2cb
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695397"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer nejčastějších dotazech

## <a name="what-types-of-load-balancer-exist"></a>Jaké typy Load Balancer existují?
Interní nástroje pro vyrovnávání zatížení, které vyrovnávají provoz v rámci virtuální sítě a externích nástrojů pro vyrovnávání zatížení, které vyrovnávají provoz z koncového bodu připojeného k Další informace najdete v tématu [typy Load Balancer](components.md#frontend-ip-configurations). 

Pro oba tyto typy nabízí Azure základní SKU a standardní SKU, které mají různé funkce pro funkční, výkon, zabezpečení a sledování stavu. Tyto rozdíly jsou vysvětleny v tomto článku o [porovnání SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Jak můžu upgradovat z úrovně Basic na Standard Load Balancer?
Další informace o automatizovaném skriptu a pokynech k upgradu Load Balancer SKU najdete v článku věnovaném [upgradu ze základního na standardní](upgrade-basic-standard.md) .

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Jaké jsou různé možnosti vyrovnávání zatížení v Azure?
V tématu [Průvodce technologií pro vyrovnávání zatížení](/azure/architecture/guide/technology-choices/load-balancing-overview)  najdete dostupné služby Vyrovnávání zatížení a doporučená použití pro každý z nich.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Kde najdu Load Balancer šablony ARM?
Přečtěte si [seznam šablon pro rychlý start Azure Load Balancer](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) pro šablony ARM běžných nasazení.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Jak se liší pravidla příchozího překladu adres (NAT) od pravidel vyrovnávání zatížení?
Pravidla překladu adres (NAT) slouží k určení prostředku back-end pro směrování provozu do. Například konfigurace konkrétního portu nástroje pro vyrovnávání zatížení pro odesílání provozu RDP na konkrétní virtuální počítač. Pravidla vyrovnávání zatížení se používají k určení fondu back-end prostředků ke směrování provozu, vyrovnání zatížení napříč jednotlivými instancemi. Například pravidlo nástroje pro vyrovnávání zatížení může směrovat pakety TCP na portu 80 nástroje pro vyrovnávání zatížení napříč fondem webových serverů.

## <a name="what-is-ip-1686312916"></a>Co je IP 168.63.129.16?
Virtuální IP adresa hostitele označeného jako infrastruktura Azure Load Balancer, kde se nacházely sondy stavu Azure. Při konfiguraci back-end instancí musí umožňovat provoz z této IP adresy úspěšné reakce na sondy stavu. Toto pravidlo nekomunikuje s přístupem k front-endu Load Balancer. Pokud Azure Load Balancer nepoužíváte, můžete toto pravidlo přepsat. Další informace o značkách služby najdete [tady](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Můžu použít globální VNET peering se základními Load Balancer?
Ne. Základní Load Balancer nepodporuje globální partnerský vztah virtuální sítě. Místo toho můžete použít Standard Load Balancer. Projděte si článek [upgrade ze základního na standardní](upgrade-basic-standard.md) článek pro bezproblémové upgradu.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Jak můžu zjistit veřejnou IP adresu, kterou virtuální počítač Azure používá?

Existuje mnoho způsobů, jak určit veřejnou zdrojovou IP adresu odchozího připojení. OpenDNS poskytuje službu, která vám umožní zobrazit veřejnou IP adresu vašeho virtuálního počítače.
Pomocí příkazu nslookup můžete odeslat dotaz DNS pro název myip.opendns.com do překladače OpenDNS. Služba vrátí zdrojovou IP adresu, která se použila k odeslání dotazu. Když z virtuálního počítače spustíte následující dotaz, odpověď je veřejná IP adresa používaná pro tento virtuální počítač:

 ```nslookup myip.opendns.com resolver1.opendns.com```

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Jak funguje připojení k Azure Storage ve stejné oblasti?
Pro připojení k úložišti ve stejné oblasti jako virtuální počítač není nutné mít odchozí připojení prostřednictvím výše uvedených scénářů. Pokud to nechcete, použijte skupiny zabezpečení sítě (skupin zabezpečení sítě), jak je vysvětleno výše. Připojení k úložišti v jiných oblastech vyžaduje odchozí připojení. Pamatujte na to, že při připojování k úložišti z virtuálního počítače ve stejné oblasti bude zdrojová IP adresa v diagnostických protokolech úložiště interní adresa poskytovatele, nikoli veřejná IP adresa vašeho virtuálního počítače. Pokud chcete omezit přístup k účtu úložiště na virtuální počítače v jedné nebo více Virtual Networkch podsítích ve stejné oblasti, při konfiguraci brány firewall účtu úložiště použijte [koncové body služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) a nemusíte mít veřejnou IP adresu. Po nakonfigurování koncových bodů služby se v diagnostických protokolech úložiště zobrazí vaše Virtual Network privátní IP adresa, nikoli adresa interního poskytovatele.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Jaké jsou nejlepší praxe s ohledem na odchozí připojení?
Standard Load Balancer a standardní veřejná IP adresa přináší možnosti a různá chování pro odchozí připojení. Nejsou stejné jako základní SKU. Pokud chcete odchozí připojení při práci se standardními SKU, musíte ho explicitně definovat buď se standardními veřejnými IP adresami, nebo se standardními veřejnými Load Balancer. To zahrnuje vytvoření odchozího připojení při použití interního Standard Load Balancer. Doporučujeme vždy používat odchozí pravidla pro standardní veřejné Load Balancer. To znamená, že když se používá interní Standard Load Balancer, musíte provést kroky pro vytvoření odchozího připojení pro virtuální počítače ve fondu back-end, pokud je potřeba odchozí připojení. V kontextu odchozího připojení, jednoho samostatného virtuálního počítače, který je ve skupině dostupnosti, se všechny instance v VMSS chovají jako skupina. To znamená, že pokud je jeden virtuální počítač ve skupině dostupnosti přidružený ke standardní SKU, všechny instance virtuálních počítačů v této skupině dostupnosti se teď budou chovat stejnými pravidly, jako kdyby byly přidružené ke standardní SKU, a to i v případě, že se k ní nepřímo přidružit samostatná instance. V případě samostatného virtuálního počítače s několika síťovými kartami připojenými k nástroji pro vyrovnávání zatížení je toto chování také pozorováno. Pokud se jedna síťová karta přidá jako samostatná, bude to mít stejné chování. Pečlivě si Projděte celý dokument, abyste porozuměli celkovým koncepcím, Projděte si [Standard Load Balancer](./load-balancer-overview.md) rozdíly mezi SKU a zkontrolujte [odchozí pravidla](load-balancer-outbound-connections.md#outboundrules).
Použití odchozích pravidel umožňuje detailní kontrolu nad všemi aspekty odchozího připojení.
 
## <a name="next-steps"></a>Další kroky
Pokud Váš dotaz není uvedený výše, pošlete nám prosím svůj názor na tuto stránku s vaším dotazem. Tím se vytvoří problém GitHubu pro produktový tým, aby se zajistila odpověď na všechny naše oceněné dotazy zákazníků.