---
title: Co je Azure DNS?
description: Přehled hostitelské služby DNS v Microsoft Azure. Hostujte svoji doménu na platformě Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: e3e04bf7e35b22a56465810f476323ed217e047a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967621"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure.

Azure DNS nelze použít k nákupu názvu domény. Název domény si můžete za roční poplatek zakoupit pomocí [Azure Web Apps](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) nebo registrátora názvu domény třetí strany. Domény pak můžete hostovat v Azure DNS za účelem správy záznamů. Další informace najdete v tématu [Delegování zón DNS s využitím Azure DNS](dns-domain-delegation.md).

Součástí Azure DNS jsou následující funkce:

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

DNS domény v Azure DNS jsou hostované na globální síti názvových serverů DNS. Azure DNS používá sítě Anycast, aby na každý dotaz DNS odpovídal nejbližší dostupný server DNS. Díky tomu bude vaše doména rychlá, výkonná i vysoce dostupná.

## <a name="security"></a>Zabezpečení

Služba Azure DNS je postavená na Azure Resource Manageru, takže získáte funkce jako například:

* [řízení přístupu na základě role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) – abyste mohli určit, kdo má přístup ke konkrétním akcím v organizaci.

* [protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) – abyste mohli monitorovat, jakým způsobem uživatel v organizaci upravil prostředek, nebo vyhledat chyby při řešení potíží.

* [uzamčení prostředku](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) – abyste mohli uzamknout předplatné, skupinu prostředků nebo prostředek a zabránit tak ostatním uživatelům v organizaci omylem odstranit nebo upravit důležité prostředky.

Další informace najdete v tématu o [ochraně záznamů a zón DNS](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Snadné používání

Služba Azure DNS může spravovat záznamy DNS vašich služeb Azure a může poskytnout DNS i externím prostředkům. Služba Azure DNS je integrovaná na webu Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře i fakturaci jako ostatní služby Azure. 

Služba Azure DNS se fakturuje na základě počtu zón DNS, které jsou v Azure hostované, a počtu dotazů DNS. Další informace o cenách najdete na stránce [Ceny za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Své domény a záznamy můžete spravovat pomocí webu Azure Portal, rutin Azure PowerShellu nebo Azure CLI pro různé platformy. Aplikace vyžadující automatizovanou správu DNS můžete do služby integrovat pomocí rozhraní REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS také podporuje privátní domény DNS, které jsou aktuálně ve verzi Public Preview. Můžete tak ve svých privátních virtuálních sítích místo aktuálně dostupných názvů poskytovaných Azure používat vlastní názvy domén.

Další informace najdete v tématu o [použití Azure DNS pro privátní domény](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Sadu záznamů aliasů můžete použít k odkazování na prostředek Azure, jako je veřejná IP adresa Azure nebo profil Traffic Managera. Pokud se změní IP adresa základního prostředku, sada záznamů aliasů se bez problémů aktualizuje během překladu DNS. Sada záznamů aliasů odkazuje na instanci služby a instance služby je spojená s IP adresou. 

Kromě toho teď můžete vrchol zóny nebo základní doménu (třeba contoso.com) odkazovat pomocí záznamu aliasu na profil Traffic Managera.

Další informace najdete v [přehledu záznamů aliasů Azure DNS](dns-alias.md).


## <a name="next-steps"></a>Další kroky

* Další informace o záznamech a zónách DNS získáte v tématu s [přehledem záznamů a zón DNS](dns-zones-records.md).

* Naučte se vytvořit zónu v Azure DNS: [Začínáme s DNS Azure pomocí webu Azure Portal](./dns-getstarted-create-dnszone-portal.md).

* Odpovědi na nejčastější dotazy týkající se Azure DNS najdete v článku s [nejčastějšími dotazy k Azure DNS](dns-faq.md).

