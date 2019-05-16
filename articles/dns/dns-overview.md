---
title: Co je Azure DNS?
description: Přehled hostitelské služby DNS v Microsoft Azure. Hostujte svoji doménu na platformě Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 530730dcf303a54803816e6f2af8787298c1c658
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65753741"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostitelská služba určená pro domény DNS, která zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure DNS nelze použít k nákupu názvu domény. Za roční poplatek, můžete si koupit názvu domény pomocí [domény služby App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) nebo registrátora názvu domén třetí strany. Domény pak můžete hostovat v Azure DNS za účelem správy záznamů. Další informace najdete v tématu [Delegování domény do DNS Azure](dns-domain-delegation.md).

Součástí Azure DNS jsou následující funkce.

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

DNS domény v Azure DNS jsou hostované na globální síti názvových serverů DNS. Azure DNS používá sítě Anycast. Na každý dotaz DNS odpovídá nejbližší dostupný server DNS. Díky tomu bude vaše doména rychlá, výkonná i vysoce dostupná.

## <a name="security"></a>Zabezpečení

 Služba Azure DNS je postavená na Azure Resource Manageru, který poskytuje například následující funkce:

* [Řízení přístupu na základě role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), abyste mohli určit, kdo má přístup ke konkrétním akcím v organizaci.

* [Protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), abyste mohli monitorovat, jakým způsobem uživatel v organizaci upravil prostředek, nebo vyhledat chyby při řešení potíží.

* [Uzamčení prostředku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources), abyste mohli uzamknout předplatné, skupinu prostředků nebo prostředek. Uzamykání zabraňuje tomu, aby ostatní uživatelé ve vaší organizaci omylem odstranili nebo změnili důležité prostředky.

Další informace najdete v tématu o [ochraně záznamů a zón DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS v současné době nepodporuje DNSSEC. Ve většině případů můžete snížit nutnost DNSSEC konzistentně pomocí protokolu HTTPS/TLS ve svých aplikacích. Pokud DNSSEC je důležité požadavky pro vaše zóny DNS, které můžete hostovat tyto zóny s poskytovateli hostingu DNS třetích stran.

## <a name="ease-of-use"></a>Snadné používání

 Azure DNS může spravovat záznamy DNS vašich služeb Azure a poskytovat DNS i externím prostředkům. Služba Azure DNS je integrovaná na webu Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře i fakturaci jako ostatní služby Azure. 

DNS se fakturuje na základě počtu zón DNS hostovaných v Azure a počtu přijatých dotazů DNS. Další informace o cenách najdete na stránce [Ceny za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Své domény a záznamy můžete spravovat pomocí webu Azure Portal, rutin Azure PowerShellu nebo Azure CLI pro různé platformy. Aplikace, které vyžadují automatizovanou správu DNS, můžete do služby integrovat pomocí rozhraní REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS podporuje také privátní domény DNS prostřednictvím funkce, která je aktuálně ve verzi Public Preview. Tato funkce umožňuje v privátních virtuálních sítích místo aktuálně dostupných názvů poskytovaných Azure používat vlastní názvy domén.

Další informace najdete v tématu [Použití Azure DNS pro privátní domény](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Sady záznamů alias můžete použít k odkazování na prostředek Azure, jako je například Azure veřejné IP adresy, profil Azure Traffic Manager nebo koncový bod Azure Content Delivery Network (CDN). Pokud se změní IP adresa základního prostředku, sada záznamů aliasů se bez problémů aktualizuje během překladu DNS. Sada záznamů aliasů odkazuje na instanci služby a instance služby je spojená s IP adresou.

Kromě toho můžete nyní ukázat vrcholu nebo základní doména profil služby Traffic Manager nebo koncový bod CDN pomocí záznamu o aliasu. Příklad: contoso.com.

Další informace najdete v [přehledu záznamů aliasů Azure DNS](dns-alias.md).

## <a name="next-steps"></a>Další postup

* Informace o záznamech a zónách DNS najdete v tématu [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o vytvoření zóny v Azure DNS najdete v tématu [Vytvoření zóny DNS](./dns-getstarted-create-dnszone-portal.md).

* Odpovědi na nejčastější dotazy týkající se Azure DNS najdete v článku s [nejčastějšími dotazy k Azure DNS](dns-faq.md).

