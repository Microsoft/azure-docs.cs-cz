---
title: Co je Azure DNS?
description: Přehled hostitelské služby DNS v Microsoft Azure. Hostujte svoji doménu na platformě Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: df890eb0e07c13d0757c706a3cabbbad67b6eac2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716272"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostitelská služba určená pro domény DNS, která zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure DNS nelze použít k nákupu názvu domény. Za roční poplatek, můžete si koupit názvu domény pomocí [domény služby App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) nebo registrátora názvu domén třetí strany. Domény pak můžete hostovat v Azure DNS za účelem správy záznamů. Další informace najdete v tématu [Delegování domény do DNS Azure](dns-domain-delegation.md).

Součástí Azure DNS jsou následující funkce.

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

DNS domény v Azure DNS jsou hostované na globální síti názvových serverů DNS. Azure DNS používá sítě Anycast. Na každý dotaz DNS odpovídá nejbližší dostupný server DNS. Díky tomu bude vaše doména rychlá, výkonná i vysoce dostupná.

## <a name="security"></a>Zabezpečení

 Služba Azure DNS je postavená na Azure Resource Manageru, který poskytuje například následující funkce:

* [Řízení přístupu na základě role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control), abyste mohli určit, kdo má přístup ke konkrétním akcím v organizaci.

* [Protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs), abyste mohli monitorovat, jakým způsobem uživatel v organizaci upravil prostředek, nebo vyhledat chyby při řešení potíží.

* [Uzamčení prostředku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources), abyste mohli uzamknout předplatné, skupinu prostředků nebo prostředek. Uzamykání zabraňuje tomu, aby ostatní uživatelé ve vaší organizaci omylem odstranili nebo změnili důležité prostředky.

Další informace najdete v tématu o [ochraně záznamů a zón DNS](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Snadné používání

 Azure DNS může spravovat záznamy DNS vašich služeb Azure a poskytovat DNS i externím prostředkům. Služba Azure DNS je integrovaná na webu Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře i fakturaci jako ostatní služby Azure. 

DNS se fakturuje na základě počtu zón DNS hostovaných v Azure a počtu přijatých dotazů DNS. Další informace o cenách najdete na stránce [Ceny za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Své domény a záznamy můžete spravovat pomocí webu Azure Portal, rutin Azure PowerShellu nebo Azure CLI pro různé platformy. Aplikace, které vyžadují automatizovanou správu DNS, můžete do služby integrovat pomocí rozhraní REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS podporuje také privátní domény DNS prostřednictvím funkce, která je aktuálně ve verzi Public Preview. Tato funkce umožňuje v privátních virtuálních sítích místo aktuálně dostupných názvů poskytovaných Azure používat vlastní názvy domén.

Další informace najdete v tématu [Použití Azure DNS pro privátní domény](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Sadu záznamů aliasů můžete použít k odkazování na prostředek Azure, jako je veřejná IP adresa Azure nebo profil služby Azure Traffic Manager. Pokud se změní IP adresa základního prostředku, sada záznamů aliasů se bez problémů aktualizuje během překladu DNS. Sada záznamů aliasů odkazuje na instanci služby a instance služby je spojená s IP adresou. 

Kromě toho teď můžete vrchol domény nebo základní doménu odkazovat pomocí záznamu aliasu na profil služby Traffic Manager. Příklad: contoso.com.

Další informace najdete v [přehledu záznamů aliasů Azure DNS](dns-alias.md).


## <a name="next-steps"></a>Další postup

* Informace o záznamech a zónách DNS najdete v tématu [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o vytvoření zóny v Azure DNS najdete v tématu [Vytvoření zóny DNS](./dns-getstarted-create-dnszone-portal.md).

* Odpovědi na nejčastější dotazy týkající se Azure DNS najdete v článku s [nejčastějšími dotazy k Azure DNS](dns-faq.md).

