---
title: Azure referenční architektura integrační služby
description: Referenční architektura znázorňující způsob implementace enterprise vzoru integrace s Logic Apps, API Management, Service Bus a Event mřížky
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232327"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Enterprise integrace s front a události: referenční architektura

## <a name="overview"></a>Přehled

Tuto referenční architekturu zobrazuje sadu osvědčené postupy pro integraci aplikace, která používá Azure integrační služby. Tato architektura může sloužit jako tento základ mnoha různých aplikací schémat vyžadující rozhraní API HTTP, pracovní postup a orchestration.

*Existuje mnoho možné aplikací integrace technologie z aplikace bodu jednoduché point-to k service bus úplné enterprise. Tato architektura řada nastaví limit opakovaně použitelné součásti pro vytváření všech integrace aplikací – architekty zvažte součásti, které budou potřebovat pro jejich aplikace a infrastrukturu.*

![Diagram architektury - enterprise integrace s front & události](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektura

Tato architektura vychází uvedeno v [jednoduché enterprise integrace](logic-apps-architectures-simple-enterprise-integration.md). Její součástí jsou následující komponenty:

- Skupina prostředků. Skupina prostředků je logický kontejner pro prostředky Azure.
- Azure API Management. Azure API Management je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní API HTTP.
- Portál pro vývojáře správy rozhraní API Azure. Každá instance služby Azure API Management se dodává s portál pro vývojáře, udělíte přístup k dokumentaci, ukázky kódu a možnost otestovat rozhraní API.
- Azure Logic Apps. Služba Logic Apps je bez serveru platformu pro sestavování enterprise pracovního postupu a integrace.
- Konektory. Konektory jsou používány Logic Apps pro připojení k běžně používaných služeb. Služba Logic Apps již stovky různých konektory, ale mohou být také vytvořeny pomocí vlastní konektor.
- Azure Service Bus. Service Bus poskytuje zabezpečený a spolehlivý zasílání zpráv. Zasílání zpráv umožňuje zrušte spojte aplikace od sebe navzájem a integraci s jinými systémy na základě zpráv.
- Azure událostí mřížky. Mřížky událostí je bez serveru platforma pro publikování a doručování událostí aplikace.
- IP adresa. Služba Azure API Management má pevnou veřejnou IP adresu a název domény. Název domény je subdoménou api.net azure, jako je například contoso.azure api.net. Aplikace logiky a Service Bus taky mít veřejnou IP adresu; ale v této architektuře jsme omezit přístup k volání koncové body aplikace logiky k jenom správy IP adres z rozhraní API (pro zabezpečení). Volání služby Service Bus jsou zabezpečeny sdílený přístupový podpis.
- Azure DNS. Azure DNS je hostitelská služba domén DNS poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Chcete-li použít vlastní název domény (například contoso.com) vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v části Konfigurace vlastního názvu domény v Azure API Management.
- Azure Active Directory (Azure AD). Použijte službu Azure AD nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k rozhraní API koncových bodů (předáním webového tokenu JSON pro správu rozhraní API pro ověření) a můžete zabezpečit přístup k portálu pro vývojáře rozhraní API správy (pouze úrovně Standard a Premium).

Tato architektura má některé základní vzorů pro své činnosti:

1. Existující back-end rozhraní API HTTP jsou publikovány prostřednictvím rozhraní API správy portál pro vývojáře, což umožňuje vývojářům (buď interní vaší organizace, externí nebo obojí) k integraci volání tato rozhraní API do aplikací.
2. Složené rozhraní API jsou vytvořeny pomocí Logic Apps; Orchestrace volání do systémů SAAS, služby Azure a všechny rozhraní API publikovat na rozhraní API Management. Aplikace logiky jsou také publikovat prostřednictvím portálu pro vývojáře správy rozhraní API.
3. Aplikace získat token OAuth 2.0 zabezpečení potřebné pro získání přístupu k rozhraní API pomocí Azure Active Directory.
4. Azure API Management ověří token zabezpečení a předá požadavek back-end aplikace API nebo logiky.
5. Fronty služby Service Bus se používají k aktivitě aplikace a smooth špičky v zatížení oddělte. Zprávy se přidají do fronty pomocí Logic Apps, 3. stran aplikace, nebo (není na obrázku) publikujete fronty jako rozhraní API HTTP přes správu rozhraní API.
6. Po přidání zprávy do fronty Service Bus, aktivuje událost. Aplikace logiky Tato událost se aktivuje a zprávu zpracuje.
7. Podobně jinými službami Azure (např. úložiště objektů Blob, centra událostí) také publikování událostí do mřížky událostí. Tyto aktivovat Logic Apps, abyste přijímat události a provádět další akce.

## <a name="recommendations"></a>Doporučení

Vaše požadavky se mohou od popsané architektury lišit. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="service-bus-tier"></a>Úroveň služby Service Bus

Použijte úroveň služby Service Bus premium, jak to aktuálně podporuje oznamování událostí mřížky (očekává se podpora napříč všechny úrovně). V tématu [ceny služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Ceny mřížky událostí

Událost mřížky funguje použití bez serveru modelu – fakturace je vypočítáváno na počet operací (provádění události). V tématu [událostí mřížky ceny](https://azure.microsoft.com/pricing/details/event-grid/) Další informace. Aktuálně neexistují žádné vrstvy důležité informace týkající se událostí mřížky.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Použití PeekLock při využívání zpráv Service Bus

Při vytváření aplikací logiky využívat zpráv Service Bus, použijte [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) v rámci aplikace logiky pro přístup k skupinu zpráv. Aplikace logiky pak můžete provádět kroky k ověření každou zprávu před dokončením nebo zrušení. Tento přístup chrání před ztrátou náhodných zprávy.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Kontrola více objektů při aktivuje aktivační procedury událostí mřížky

Aktivační události mřížky ohlásí jednoduše znamená, že "alespoň 1 z těchto akcí se stalo". Například když mřížky událost se aktivuje aplikaci logiky ve zprávě zobrazovaných v fronty Service Bus, aplikaci logiky měli vždy předpokládají, že může být jeden nebo více zpráv, které jsou k dispozici pro zpracování.

### <a name="region"></a>Oblast

Zřídit API Management, Logic Apps a Service Bus ve stejné oblasti minimalizaci latence sítě. Vyberte oblast, která je vašim uživatelům nejbližší.

Skupina prostředků má také oblast, která určuje, kde se ukládají metadata nasazení, a kde se šablona nasazení se spustí z. Skupinu prostředků a její prostředky umístěte do stejné oblasti. Zlepšíte tím dostupnost během nasazení.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

Úroveň premium Azure Service Bus můžete Škálováním na více systémů počet jednotek zasílání zpráv k dosažení vyšší škálovatelnost. Premium může mít 1, 2 nebo 4 jednotky zasílání zpráv. Další informace na škálování Azure Service Bus, najdete v části [osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

V době psaní smlouvu o úrovni služeb (SLA) pro Azure API Management je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazením alespoň jednu jednotku ve dvou nebo více oblastech mít SLA 99,95 %.

V době psaní smlouvu o úrovni služeb (SLA) pro Azure Logic Apps je 99,9 %.

### <a name="disaster-recovery"></a>Zotavení po havárii

Zvažte implementaci geograficky havárii v Service Bus premium povolit převzetí služeb při selhání v případě závažných výpadku. Další informace o [Azure Service Bus Geo-havárii](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Aspekty správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoj a testování prostředí. Usnadníte tak správu nasazení, odstranění nasazení v testovacím prostředí a přiřazení přístupových práv.
Při přiřazování prostředků skupinám prostředků zvažte následující:

- Životní cyklus. Prostředky se stejným životním cyklem umístěte do stejné skupiny prostředků.
- Přístup. Můžete použít [řízení přístupu na základě Role](../role-based-access-control/overview.md) (RBAC) Chcete-li použít zásady přístupu k prostředkům ve skupině.
- Fakturace. Můžete zobrazit souhrnné náklady na skupinu prostředků.
- Cenovou úroveň pro API Management – doporučujeme použít úroveň Developer pro vývojové a testovací prostředí. Pro předprodukční doporučujeme nasazení repliku produkčního prostředí, spouštění testů a pak vypínání minimalizovat náklady.

Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) Přehled.

### <a name="deployment"></a>Nasazení

Doporučujeme vám, že používáte [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazovat Azure API Management, Logic Apps, události mřížky a Service Bus. Šablony usnadňují automatizaci nasazení přes PowerShell nebo rozhraní příkazového řádku Azure (CLI).

Doporučujeme, abyste uvedení v vlastní samostatné šablony správce prostředků Azure API Management, všechny jednotlivé Logic Apps, události mřížky témata a obory názvů Service Bus. To vám umožní uložit je do zdrojových systémů řízení pro. Tyto šablony se dá nasadit pak společně nebo samostatně jako součást nepřetržité integrace/průběžné (CI/CD) procesu nasazení.

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Service Bus, jako je Správa rozhraní API a Logic Apps je možné monitorovat pomocí monitorování Azure. Azure monitorování je ve výchozím nastavení povolené a poskytne informace, na základě různých metrik nakonfigurována pro každou službu.

## <a name="security-considerations"></a>Aspekty zabezpečení

Zabezpečení služby Service Bus pomocí sdíleného přístupového podpisu. [Ověřování SAS](../service-bus-messaging/service-bus-sas.md) vám umožní udělit přístup uživatelů k prostředkům služby Service Bus, se konkrétní práva. Další informace o [Service Bus ověřování a autorizace](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Kromě toho by měla fronty Service Bus potřeba zpřístupnit jako koncový bod HTTP (aby bylo možné zveřejňování nové zprávy), rozhraní API Management se má použít k zabezpečení podle fronting koncový bod. To můžete pak zabezpečit pomocí certifikátů nebo OAuth podle potřeby. Nejjednodušší způsob je pomocí aplikace logiky aktivační událost požadavků a odpovědí HTTP jako prostředník.

Událost mřížky zabezpečuje doručení událostí prostřednictvím ověřovacího kódu. Pokud používáte LogicApps využívat události, se provádí automaticky. Zobrazte další podrobnosti [mřížky událostí zabezpečení a ověřování](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Další postup

* [Integrace jednoduché Enterprise](logic-apps-architectures-simple-enterprise-integration.md)
