---
title: Referenční architektura Azure integrační služby
description: Referenční architektura ukazuje, jak implementovat vzor podnikové integrace s Logic Apps, API Management, Service Bus a Event Grid
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
ms.openlocfilehash: 7d14bbd587b5086348026c41f6551b10809b939a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859638"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Podniková integrace pomocí front a události: referenční architektura

## <a name="overview"></a>Přehled

Tato referenční architektura představuje sadu osvědčených postupů pro integraci aplikace, která využívá integrace služeb Azure. Tato architektura může sloužit jako tento základ mnoha vzory jinou aplikaci vyžadující rozhraní API HTTP, pracovních postupů a Orchestrace.

*Existuje mnoho možných aplikací integrace technologie z bodu aplikace jednoduchý point-to na úplné podnikové služby Service bus. Tato architektura řada stanoví opakovaně použitelné součásti, které může použít pro sestavení aplikace obecný integrace – architekty zvažte, jaké konkrétní komponenty, bude nutné implementovat pro svoje aplikace a infrastrukturu.*

![Diagram architektury – podniková integrace s frontami a události](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektura

Tato architektura **vychází** [jednoduché podniková integrace](logic-apps-architectures-simple-enterprise-integration.md) architektury. [Jednoduché podnikové architektury doporučení](logic-apps-architectures-simple-enterprise-integration.md#recommendations) i tady platí, ale bylo vynecháno v [doporučení](#recommendations) v tomto dokumentu pro zkrácení. Má následující komponenty:

- Skupina prostředků. [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) je logický kontejner prostředků Azure.
- Azure API Management. [Azure API Management](https://docs.microsoft.com/azure/api-management/) je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní HTTP API.
- Portál pro vývojáře Azure API Management. Každá instance Azure API Management obsahuje [portál pro vývojáře](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), poskytne přístup k dokumentaci, ukázky kódu a možnost otestovat rozhraní API.
- Azure Logic Apps. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) je platforma bez serveru pro tvorbu podnikových pracovního postupu a integrace.
- Konektory. [Konektory](https://docs.microsoft.com/azure/connectors/apis-list) se používají v Logic Apps pro připojení k nejčastěji používané služby. Už má stovky různých konektory Logic Apps, ale mohou být také vytvořeny pomocí vlastního konektoru.
- Azure Service Bus. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) poskytuje zabezpečené a spolehlivé zasílání zpráv. Zasílání zpráv je možné zrušit spárovat aplikace od sebe a integraci s jinými systémy založené na zprávách.
- Azure Event Grid. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) je platforma pro publikování a doručování událostí aplikace bez serveru.
- IP adresa. Služba Azure API Management má pevné veřejné [IP adresu](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) a názvu domény. Název domény je subdoména azure-api.net, jako je například contoso.azure-api.net. Logic Apps a služby Service Bus taky mít veřejnou IP adresu. ale v této architektuře Omezujeme přístup k volání koncových bodů aplikace logiky pro pouze správy IP adres z rozhraní API (pro zabezpečení). Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu.
- Azure DNS. [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Chcete-li použít vlastní název domény (například contoso.com) vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v části Konfigurace vlastního názvu domény ve službě Azure API Management.
- Azure Active Directory (Azure AD). Použití [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování ke koncovým bodům přístup k rozhraní API (předáním [webového tokenu JSON pro službu API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ověřit) a může zabezpečit přístup k portálu pro vývojáře pro správu rozhraní API (jenom úrovně Standard a Premium).

Tato architektura se některých základních vzorů pro své činnosti:

1. Stávající back-endového rozhraní API HTTP jsou publikované prostřednictvím rozhraní API pro vývojáře portálu pro správu, který vývojářům umožňuje (buď interně ve vaší organizaci, externí nebo obojí) k integraci volání těchto rozhraní API do aplikací.
2. Složený rozhraní API se vytvářejí pomocí Logic Apps; Orchestrace volání k systémům SAAS, služby Azure a libovolné rozhraní API publikované do API managementu. [Logic Apps se také publikují](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) prostřednictvím portálu pro vývojáře rozhraní API pro správu.
3. Aplikace [získání tokenu zabezpečení OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) nezbytné pro získání přístupu k rozhraní API pomocí Azure Active Directory.
4. Azure API Management [ověří token zabezpečení](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)a předá požadavek back-endové aplikace API a logiky.
5. Fronty služby Service Bus se používají pro [oddělit](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) aktivita aplikace a [vyhlazení špiček v zatížení](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Zprávy se přidá do fronty v Logic Apps, 3. stran aplikace, nebo (bez obrázku) a publikujte do fronty v rozhraní API HTTP prostřednictvím služby API Management.
6. Při přidání zprávy do fronty služby Service Bus, aktivuje událost. Aplikace logiky se tato událost aktivuje a zpracovává zprávy.
7. Podobně další služby Azure (např. úložiště objektů Blob, centra událostí) také publikování událostí do služby Event Grid. Tyto aktivovat Logic Apps přijímat události a provádět další akce.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře zde popsané. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="service-bus-tier"></a>Úroveň služby Service Bus

Použití služby Service Bus úrovně premium jako to aktuálně podporuje event grid oznámení (očekává se podpora ve všech vrstvách). Zobrazit [cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Cenách služby Event Grid

Event Grid funguje pomocí modelu bez serveru – fakturace se vypočítá podle počtu operací (provádění události). Zobrazit [ceny služby Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) Další informace. Aktuálně neexistují žádné požadavky na úroveň pro Event Grid.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Použití PeekLock při využívání zpráv služby Service Bus

Při vytváření aplikací logiky využívat zprávy služby Service Bus, použijte [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) v rámci aplikace logiky pro přístup ke skupině zpráv. Aplikace logiky můžete provést kroky k ověření každou zprávu před dokončení nebo zrušení. Tento přístup chrání před ztrátou náhodného zprávy.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Vyhledat více objektů, když se aktivuje trigger Event Grid

Aktivační události mřížky ohlásí jednoduše znamená, že "došlo k nejméně 1 z těchto věcí". Například když Event Grid aktivuje aplikace logiky podle povolí, fronta služby Service Bus zprávu, aplikace logiky by měla vždy předpokládat, že může být jeden nebo více zpráv, které jsou k dispozici pro zpracování.

### <a name="region"></a>Oblast

Zřizování API Management a Logic Apps a služby Service Bus ve stejné oblasti, které kvůli minimalizaci latence sítě. Vyberte oblast, která je vašim uživatelům nejbližší.

Skupina prostředků má také oblast, která určuje, kde se ukládají metadata nasazení, kde se spouštějí šablonu nasazení z. Skupinu prostředků a její prostředky umístěte do stejné oblasti. Zlepšíte tím dostupnost během nasazení.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

Azure Service Bus úrovně premium můžete horizontálním navýšením počtu jednotek zasílání zpráv, abyste dosáhli vyšší škálovatelnost. Úrovní Premium můžou mít 1, 2 nebo 4 jednotky zasílání zpráv. Další informace o škálování služby Azure Service Bus, najdete v části [osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

V době psaní smlouvu o úrovni služeb (SLA) pro službu Azure API Management je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazení nejméně jedné jednotky ve dvou nebo více oblastech mají smlouvou SLA zajišťující 99,95 %.

V době psaní smlouvu o úrovni služeb (SLA) pro Azure Logic Apps je 99,9 %.

### <a name="disaster-recovery"></a>Zotavení po havárii

Implementace zotavení po havárii geograficky v Service Bus úrovně premium, umožňuje převzetí služeb při selhání v případě závažných výpadku. Další informace o [Azure Service Bus Geo-zotavení po havárii](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Aspekty správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Usnadníte tak správu nasazení, odstranění nasazení v testovacím prostředí a přiřazení přístupových práv.
Při přiřazování prostředků skupinám prostředků zvažte následující:

- Životní cyklus. Prostředky se stejným životním cyklem umístěte do stejné skupiny prostředků.
- Přístup. Můžete použít [řízení přístupu na základě Role](../role-based-access-control/overview.md) (RBAC) k uplatnění zásad přístupu k prostředkům ve skupině.
- Fakturace. Můžete zobrazit souhrnné náklady na skupinu prostředků.
- Cenová úroveň pro službu API Management – doporučujeme použít úroveň Developer pro vývojová a testovací prostředí. Pro předprodukční režim doporučujeme nasadit repliku produkčního prostředí, spouštění testů a vypínání pro minimalizaci nákladů.

Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) Přehled.

### <a name="deployment"></a>Nasazení

Doporučujeme, abyste použili [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazení Azure API Management, Logic Apps, Service Bus a Event Grid. Šablony usnadňují automatizaci nasazení prostřednictvím Powershellu nebo rozhraní příkazového řádku Azure (CLI).

Doporučujeme, abyste uvedení Azure API Management, všechny individuální Logic Apps, tématy Event gridu a obory názvů služby Service Bus v jejich vlastní samostatné šablony Resource Manageru. To vám umožní ukládání v systémy správy zdrojového kódu. Tyto šablony můžete nasadit pak společně nebo samostatně jako součást procesu nasazení s nepřetržitou integrací (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Service Bus, jako je API Management a Logic Apps je možné monitorovat pomocí Azure monitoru. Azure Monitor je ve výchozím nastavení povolené a bude poskytovat informace na základě různých metrik nakonfigurované pro každou službu.

## <a name="security-considerations"></a>Aspekty zabezpečení

Zabezpečení služby Service Bus pomocí sdíleného přístupového podpisu. [Ověřování SAS](../service-bus-messaging/service-bus-sas.md) vám umožní udělit přístup uživatelů k prostředkům služby Service Bus se konkrétní práva. Další informace o [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Kromě toho by mělo být fronty Service Bus potřeba vystavit jako koncový bod HTTP (abyste měli zveřejnění nové zprávy), API Management má použít k zabezpečení podle popředí koncový bod. To je pak možné svázat s certifikáty nebo OAuth podle potřeby. Nejjednodušší způsob, jak to provést používá aplikace logiky s triggerem požadavku nebo odpovědi HTTP jako prostředník.

Event Grid zabezpečuje doručování událostí prostřednictvím ověřovacího kódu. Používáte-li využívají události LogicApps, se provádí automaticky. Zobrazit další podrobnosti o [ověřování a zabezpečení služby Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Další postup

- [Jednoduché podniková integrace](logic-apps-architectures-simple-enterprise-integration.md)