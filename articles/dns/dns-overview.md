---
title: Co je Azure DNS?
description: Přehled hostitelské služby DNS v Microsoft Azure. Hostujte svoji doménu na platformě Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: 1543c0daae7d637730a5f8f9da2305423ba7f84e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76932409"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostitelská služba určená pro domény DNS, která zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure DNS nelze použít k nákupu názvu domény. Za roční poplatek si můžete koupit název domény pomocí [domén služby App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) nebo registrátora domén třetích stran. Domény pak můžete hostovat v Azure DNS za účelem správy záznamů. Další informace najdete [v tématu Delegování domény na Azure DNS](dns-domain-delegation.md).

Součástí Azure DNS jsou následující funkce.

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

DNS domény v Azure DNS jsou hostované na globální síti názvových serverů DNS. Azure DNS používá sítě Anycast. Na každý dotaz DNS odpovídá nejbližší dostupný server DNS. Díky tomu bude vaše doména rychlá, výkonná i vysoce dostupná.

## <a name="security"></a>Zabezpečení

 Služba Azure DNS je postavená na Azure Resource Manageru, který poskytuje například následující funkce:

* [Řízení přístupu na základě role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), abyste mohli určit, kdo má přístup ke konkrétním akcím v organizaci.

* [Protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), abyste mohli monitorovat, jakým způsobem uživatel v organizaci upravil prostředek, nebo vyhledat chyby při řešení potíží.

* [Uzamčení prostředku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources), abyste mohli uzamknout předplatné, skupinu prostředků nebo prostředek. Uzamykání zabraňuje tomu, aby ostatní uživatelé ve vaší organizaci omylem odstranili nebo změnili důležité prostředky.

Další informace najdete v tématu o [ochraně záznamů a zón DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>Dnssec

Azure DNS aktuálně nepodporuje DNSSEC. Ve většině případů můžete snížit potřebu DNSSEC konzistentně pomocí HTTPS/TLS ve vašich aplikacích. Pokud je dnssec kritickým požadavkem pro vaše zóny DNS, můžete tyto zóny hostovat s poskytovateli hostingu DNS třetích stran.

## <a name="ease-of-use"></a>Snadné používání

 Azure DNS může spravovat záznamy DNS vašich služeb Azure a poskytovat DNS i externím prostředkům. Služba Azure DNS je integrovaná na webu Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře i fakturaci jako ostatní služby Azure. 

DNS se fakturuje na základě počtu zón DNS hostovaných v Azure a počtu přijatých dotazů DNS. Další informace o cenách najdete na stránce [Ceny za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Své domény a záznamy můžete spravovat pomocí webu Azure Portal, rutin Azure PowerShellu nebo Azure CLI pro různé platformy. Aplikace, které vyžadují automatizovanou správu DNS, můžete do služby integrovat pomocí rozhraní REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS také podporuje privátní domény DNS. Tato funkce umožňuje v privátních virtuálních sítích místo aktuálně dostupných názvů poskytovaných Azure používat vlastní názvy domén.

Další informace najdete v tématu [Použití Azure DNS pro privátní domény](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Sadu záznamů aliasu můžete použít k odkazování na prostředek Azure, jako je veřejná IP adresa Azure, profil Azure Traffic Manager nebo koncový bod Sítě pro doručování obsahu Azure (CDN). Pokud se změní IP adresa základního prostředku, sada záznamů aliasů se bez problémů aktualizuje během překladu DNS. Sada záznamů aliasů odkazuje na instanci služby a instance služby je spojená s IP adresou.

Nyní můžete také nasměrovat svou vrcholnou nebo holou doménu na profil Traffic Manageru nebo koncový bod CDN pomocí záznamu aliasu. Příklad: contoso.com.

Další informace najdete v [přehledu záznamů aliasů Azure DNS](dns-alias.md).

## <a name="next-steps"></a>Další kroky

* Informace o záznamech a zónách DNS najdete v tématu [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o vytvoření zóny v Azure DNS najdete v tématu [Vytvoření zóny DNS](./dns-getstarted-create-dnszone-portal.md).

* Odpovědi na nejčastější dotazy týkající se Azure DNS najdete v článku s [nejčastějšími dotazy k Azure DNS](dns-faq.md).

