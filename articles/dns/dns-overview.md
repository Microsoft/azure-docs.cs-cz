---
title: Co je Azure DNS?
description: Přehled služby hostování DNS v Microsoft Azure. Hostování vaší domény v Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 5055734ae3529d7fca87688c5f9410f38368a634
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959368"
---
# <a name="what-is-azure-dns"></a>Co je Azure DNS?

Azure DNS je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí Microsoft Azure infrastruktury. Hostováním domén v Azure můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure.

K nákupu názvu domény nemůžete použít Azure DNS. V případě ročního poplatku můžete koupit název domény pomocí [App Service domény](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) nebo registrátora názvu domény třetí strany. Vaše domény pak můžou být hostované v Azure DNS pro správu záznamů. Další informace najdete v tématu [delegování domény na Azure DNS](dns-domain-delegation.md).

Následující funkce jsou součástí Azure DNS.

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

Domény DNS v Azure DNS hostují globální síť názvových serverů DNS v Azure. Azure DNS používá síť s více vysíláními. Každý dotaz DNS je zodpovězený nejbližším dostupným serverem DNS, který poskytuje rychlý výkon a vysokou dostupnost vaší domény.

## <a name="security"></a>Zabezpečení

 Azure DNS je založená na Azure Resource Manager, která poskytuje funkce, jako například:

* [Řízení přístupu na základě role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , které řídí, kdo má přístup ke konkrétním akcím pro vaši organizaci.

* [Protokoly aktivit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , které sledují, jak uživatel ve vaší organizaci upravil prostředek, nebo aby při řešení potíží nalezl chybu.

* [Uzamykání prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) pro zamčení předplatného, skupiny prostředků nebo prostředku. Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků.

Další informace najdete v tématu [ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>PROTOKOLU

Azure DNS aktuálně nepodporuje DNSSEC. Ve většině případů můžete snížit nutnost rozšíření DNSSEC tím, že ve svých aplikacích konzistentně použijete HTTPS/TLS. Pokud je DNSSEC zásadním požadavkem pro zóny DNS, můžete tyto zóny hostovat s poskytovateli hostování DNS třetích stran.

## <a name="ease-of-use"></a>Snadné použití

 Azure DNS může spravovat záznamy DNS pro služby Azure a poskytovat taky DNS pro vaše externí prostředky. Azure DNS je integrován v Azure Portal a používá stejné přihlašovací údaje, smlouvu o podpoře a vyúčtování jako jiné služby Azure. 

Fakturace DNS vychází z počtu zón DNS hostovaných v Azure a počtu přijatých dotazů DNS. Další informace o cenách najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/).

Vaše domény a záznamy můžete spravovat pomocí Azure Portal, Azure PowerShell rutin a Azure CLI pro různé platformy. Aplikace, které vyžadují automatizovanou správu DNS, se dají integrovat se službou pomocí REST API a sad SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Přizpůsobitelné virtuální sítě s privátními doménami

Azure DNS podporuje i privátní domény DNS. Tato funkce umožňuje používat ve svých privátních virtuálních sítích vlastní názvy domén místo těch, které jsou dnes k dispozici pro Azure.

Další informace najdete v tématu [použití Azure DNS u privátních domén](private-dns-overview.md).

## <a name="alias-records"></a>Záznamy aliasů

Azure DNS podporuje sady záznamů aliasů. Pomocí sady záznamů aliasů můžete odkazovat na prostředek Azure, jako je například veřejná IP adresa Azure, profil Azure Traffic Manager nebo koncový bod Azure Content Delivery Network (CDN). Pokud se změní IP adresa podkladového prostředku, sada záznamů alias se během překladu názvů DNS bezproblémově aktualizuje. Sada záznamů s aliasem odkazuje na instanci služby a instance služby je přidružená k IP adrese.

Nyní můžete na vrchol nebo zaholé domény nasměrovat Traffic Manager profil nebo adresu CDN pomocí záznamu aliasu. Příkladem je contoso.com.

Další informace najdete v tématu [přehled Azure DNS záznamů aliasů](dns-alias.md).

## <a name="next-steps"></a>Další kroky

* Další informace o zónách a záznamech DNS najdete v tématu [Přehled zón a záznamů DNS](dns-zones-records.md).

* Informace o tom, jak vytvořit zónu v Azure DNS, najdete v tématu [vytvoření zóny DNS](./dns-getstarted-create-dnszone-portal.md).

* Nejčastější dotazy týkající se Azure DNS najdete v části [Nejčastější dotazy k Azure DNS](dns-faq.md).

