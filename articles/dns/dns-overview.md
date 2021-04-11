---
title: Co je Azure DNS?
description: Přehled hostitelské služby DNS v Microsoft Azure. Hostujte svoji doménu na platformě Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/25/2021
ms.author: rohink
ms.openlocfilehash: 41d2fdca653828588a5881daa0fa16c3bc71b5c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046483"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostitelská služba pro domény DNS, která poskytuje překlad názvů na IP adresy pomocí infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure DNS nelze použít k nákupu názvu domény. V případě ročního poplatku můžete koupit název domény pomocí [App Service domény](../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain) nebo registrátora názvu domény třetí strany. Domény pak můžete hostovat v Azure DNS za účelem správy záznamů. Další informace najdete v tématu [delegování domény na Azure DNS](dns-domain-delegation.md).

Součástí Azure DNS jsou následující funkce.

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

DNS domény v Azure DNS jsou hostované na globální síti názvových serverů DNS. Azure DNS používá sítě Anycast. Na každý dotaz DNS odpovídá nejbližší dostupný server DNS. Díky tomu bude vaše doména rychlá, výkonná i vysoce dostupná.

## <a name="security"></a>Zabezpečení

 Služba Azure DNS je postavená na Azure Resource Manageru, který poskytuje například následující funkce:

* [Řízení přístupu na základě role Azure (Azure RBAC)](../azure-resource-manager/management/overview.md) k řízení, kdo má přístup ke konkrétním akcím pro vaši organizaci.

* [Protokoly aktivit](../azure-resource-manager/management/overview.md), abyste mohli monitorovat, jakým způsobem uživatel v organizaci upravil prostředek, nebo vyhledat chyby při řešení potíží.

* [Uzamčení prostředku](../azure-resource-manager/management/lock-resources.md), abyste mohli uzamknout předplatné, skupinu prostředků nebo prostředek. Uzamykání zabraňuje tomu, aby ostatní uživatelé ve vaší organizaci omylem odstranili nebo změnili důležité prostředky.

Další informace najdete v tématu o [ochraně záznamů a zón DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>PROTOKOLU

Azure DNS aktuálně nepodporuje DNSSEC. Ve většině případů můžete snížit nutnost rozšíření DNSSEC tím, že ve svých aplikacích konzistentně použijete HTTPS/TLS. Pokud je DNSSEC zásadním požadavkem pro zóny DNS, můžete tyto zóny hostovat s poskytovateli hostování DNS třetích stran.

## <a name="ease-of-use"></a>Jednoduché používání

 Azure DNS může spravovat záznamy DNS vašich služeb Azure a poskytovat DNS i externím prostředkům. Služba Azure DNS je integrovaná na webu Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře i fakturaci jako ostatní služby Azure. 

DNS se fakturuje na základě počtu zón DNS hostovaných v Azure a počtu přijatých dotazů DNS. Další informace o cenách najdete na stránce [Ceny za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Své domény a záznamy můžete spravovat pomocí webu Azure Portal, rutin Azure PowerShellu nebo Azure CLI pro různé platformy. Aplikace, které vyžadují automatizovanou správu DNS, se dají integrovat se službou pomocí rozhraní REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS podporuje i privátní domény DNS. Tato funkce umožňuje v privátních virtuálních sítích místo aktuálně dostupných názvů poskytovaných Azure používat vlastní názvy domén.

Další informace najdete v tématu [Použití Azure DNS pro privátní domény](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Pomocí sady záznamů aliasů můžete odkazovat na prostředek Azure, jako je například veřejná IP adresa Azure, profil Azure Traffic Manager nebo koncový bod Azure Content Delivery Network (CDN). Pokud se změní IP adresa základního prostředku, sada záznamů aliasů se bez problémů aktualizuje během překladu DNS. Sada záznamů aliasů odkazuje na instanci služby a instance služby je spojená s IP adresou.

Nyní můžete na vrchol nebo zaholé domény nasměrovat Traffic Manager profil nebo adresu CDN pomocí záznamu aliasu. Příklad: contoso.com.

Další informace najdete v [přehledu záznamů aliasů Azure DNS](dns-alias.md).

## <a name="next-steps"></a>Další kroky

* Informace o záznamech a zónách DNS najdete v tématu [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o vytvoření zóny v Azure DNS najdete v tématu [Vytvoření zóny DNS](./dns-getstarted-portal.md).

* Odpovědi na nejčastější dotazy týkající se Azure DNS najdete v článku s [nejčastějšími dotazy k Azure DNS](dns-faq.md).
