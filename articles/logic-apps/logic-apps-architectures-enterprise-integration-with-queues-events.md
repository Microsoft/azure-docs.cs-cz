---
title: Integrační služby enterprise integration referenční architektura Azure
description: Popisuje referenční architektury, který ukazuje, jak implementovat vzor podnikové integrace pomocí Logic Apps, API Management, Service Bus a Event Grid.
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116108"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Referenční architektura: podniková integrace pomocí front a události

Tato referenční architektura ukazuje sadu osvědčených postupů, které můžete použít k integraci aplikace, která využívá integrace služeb Azure. Tato architektura může sloužit jako základ pro mnoho různých aplikačních vzory, které vyžadují rozhraní API HTTP, pracovních postupů a Orchestrace.

![Diagram architektury – podniková integrace pomocí front a události](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Existuje mnoho možných aplikací pro integraci technologie. Jsou v rozsahu od jednoduché point-to-point aplikace k úplné podnikové aplikace Azure Service Bus. Architektura série popisuje opakovaně použitelné součásti, které může použít k sestavení aplikace obecný integrace. Architekti zvažte komponenty, které potřebují k implementaci pro své aplikace a infrastrukturu.*

## <a name="architecture"></a>Architektura

Architektura *vychází* [jednoduché podniková integrace](logic-apps-architectures-simple-enterprise-integration.md) architektury. [Doporučení pro jednoduché podnikové architektury](logic-apps-architectures-simple-enterprise-integration.md#recommendations) zde použít. Jsou vynechány z [doporučení](#recommendations) v tomto článku pro zkrácení. 

Tato architektura se skládá z následujících komponent:

- **Skupina prostředků**. [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) je logický kontejner prostředků Azure.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/) je plně spravovaná platforma, která se používá k publikování, zabezpečení a transformujte rozhraní API protokolu HTTP.
- **Portál Azure API Management Developer**. Každá instance Azure API Management obsahuje přístup k [portál pro vývojáře](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Portál pro vývojáře rozhraní API Management poskytuje přístup k dokumentaci a ukázky kódu. Testování rozhraní API na portálu pro vývojáře.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) je platformy bez serveru, který se používá k vytvoření pracovního postupu enterprise a integrace.
- **Konektory**. Logic Apps používá [konektory](https://docs.microsoft.com/azure/connectors/apis-list) pro připojení k nejčastěji používají služby. Už má stovky různých konektory Logic Apps, ale můžete také vytvořit vlastní konektor.
- **Azure Service Bus**. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) poskytuje zabezpečené a spolehlivé zasílání zpráv. Zasílání zpráv je možné oddělit aplikace a zajistit integraci s jinými systémy založené na zprávách.
- **Azure Event Grid**. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) je platformy bez serveru, který se používá k publikování a doručení událostí aplikace.
- **IP adresa**. Služba Azure API Management má pevné veřejné [IP adresu](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) a názvu domény. Název domény je subdoména azure-api.net, jako je například contoso.azure-api.net. Logic Apps a služby Service Bus taky mít veřejnou IP adresu. Ale v této architektuře Omezujeme přístup k volání koncových bodů aplikace logiky na IP adresu služby API Management (pro zabezpečení). Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu (SAS).
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS. Azure DNS poskytuje překlad názvů s využitím infrastruktury Microsoft Azure. Hostovat v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturační účely ostatních služeb Azure. Pokud chcete použít vlastní název domény (např. contoso.com), vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v tématu [konfigurace vlastního názvu domény ve službě API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD):** Použití [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k koncových bodů rozhraní API pomocí předání [webového tokenu JSON pro službu API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) k ověření. Azure AD můžete zabezpečit přístup k portálu pro vývojáře rozhraní API Management (jenom úrovně Standard a Premium).

Tato architektura se některé vzory, které jsou zásadní pro její činnosti:

* Stávající back endové rozhraní API HTTP publikují prostřednictvím portálu pro vývojáře rozhraní API Management. Na portálu pro vývojáře (buď interně ve vaší organizaci, externí nebo obojí) volání těchto rozhraní API můžete integrovat do aplikací.
* Složený rozhraní API jsou vytvořené pomocí logic apps a tím, že orchestruje volání software jako služba (SaaS) systémů, služeb Azure a libovolné rozhraní API, které jsou publikovány do API managementu. [Aplikace logiky se také publikují](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) prostřednictvím portálu pro vývojáře rozhraní API Management.
- Aplikace použít Azure AD, aby [získání tokenu zabezpečení OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , který je potřeba k získání přístupu k rozhraní API.
- API Management [ověří token zabezpečení](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) a pak předá požadavek back endové rozhraní API nebo logiku aplikace.
- Fronty služby Service Bus se používají pro [oddělit](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) aktivita aplikace a [vyhlazení špiček v zatížení](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Zprávy se přidá do fronty v logic apps, aplikace třetích stran, nebo (bez obrázku) a publikujte do fronty v rozhraní API HTTP prostřednictvím služby API Management.
- Při přidání zprávy do fronty služby Service Bus, aktivuje událost. Aplikace logiky se aktivuje událost. Aplikace logiky pak zpracovává zprávy.
- Další služby Azure (třeba Azure Blob storage a Azure Event Hubs) také publikování událostí do služby Event Grid. Tyto služby spouštění logiky aplikace přijímat události a pak proveďte následující akce.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře, která je popsána v tomto článku. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="service-bus-tier"></a>Úroveň služby Service Bus

Použijte na úrovni služby Service Bus úrovně Premium. Premier úrovně podporuje oznámení služby Event Grid. Další informace najdete v tématu [cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Cenách služby Event Grid

Event Grid používá model bez serveru. Fakturace se vypočítá podle počet operací (provádění události). Další informace najdete v tématu [ceny služby Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). V současné době nejsou k dispozici žádné úrovně důležité informace týkající se služby Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Pomocí PeekLock využívat zprávy služby Service Bus

Když vytvoříte aplikaci logiky, která využívat zprávy služby Service Bus, použijte [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) v aplikaci logiky pro přístup ke skupině zpráv. Při použití PeekLock aplikace logiky může provést kroky k ověření každou zprávu před dokončením nebo opuštění. Tento přístup chrání před ztrátou náhodného zprávy.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Vyhledat více objektů, když se aktivuje trigger Event Grid

Event Grid trigger aktivuje, znamená to, že jednoduše, že "splnit aspoň jednu z těchto věcí ke vzniku." Předpokládejme například, když Event Grid aktivuje aplikace logiky na zprávu, která se zobrazí ve frontě služby Service Bus, aplikace logiky by měla vždy, může být jeden nebo více zpráv, které jsou k dispozici pro zpracování.

### <a name="region"></a>Oblast

Zřizování API Management a Logic Apps a služby Service Bus ve stejné oblasti, kvůli minimalizaci latence sítě. Obecně volte oblast co nejblíže vašim uživatelům.

Skupina prostředků má také oblast. Oblast určuje, kde se ukládají metadata nasazení a kde šablonu nasazení spustí. Skupinu prostředků a její prostředky umístěte ve stejné oblasti vylepšit dostupnost během nasazení.

## <a name="scalability"></a>Škálovatelnost

Na úrovni služby Service Bus Premium můžete škálovat počet jednotek zasílání zpráv, abyste dosáhli vyšší škálovatelnost. Konfigurace úrovně Premium může mít jednu, dvě nebo čtyři jednotky zasílání zpráv. Další informace o škálování služby Service Bus, najdete v části [osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Dostupnost

Smlouvu o úrovni služeb (SLA) pro službu Azure API Management v současné době je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazení nejméně jedné jednotky ve dvou nebo více oblastech mají smlouvou SLA zajišťující 99,95 %.

Smlouva SLA pro Azure Logic Apps se v současné době 99,9 %.

### <a name="disaster-recovery"></a>Zotavení po havárii

Implementace zotavení po havárii geograficky v Service Bus úrovně Premium umožňuje převzetí služeb při selhání, pokud dojde k závažné výpadku. Další informace najdete v tématu [geografického zotavení Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Možnosti správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Samostatné skupiny prostředků usnadňuje správu nasazení, odstranění nasazení v testovacím a přiřadit přístupová práva.

Při přiřazování prostředků skupinám prostředků zvažte následující faktory:

- **Životní cyklus**. Obecně platí vložte prostředky, které mají stejný životní cyklus ve stejné skupině prostředků.
- **Přístup**. Můžete použít [řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) k uplatnění zásad přístupu k prostředkům ve skupině.
- **Fakturace**. Můžete zobrazit souhrnné náklady pro skupinu prostředků.
- **Cenová úroveň pro službu API Management**. Doporučujeme používat úroveň Developer pro vývoj a testovací prostředí. Pro předprodukční prostředí doporučujeme nasadit repliku produkčního prostředí, spouštění testů a vypínání pro minimalizaci nákladů.

Další informace naleznete v tématu [Přehled Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Nasazení

Doporučujeme, abyste použili [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazení API managementu, Logic Apps, služby Event Grid a Service Bus. Šablony usnadňují automatizaci nasazení prostřednictvím Powershellu nebo rozhraní příkazového řádku Azure.

Doporučujeme, abyste svoje vlastní, samostatné šablony Resource Manageru uvedením API Management, všechny individuální logic apps, tématy Event gridu a obory názvů služby Service Bus. Když použijete samostatné šablony, můžete ukládat prostředky v systémy správy zdrojového kódu. Tyto šablony můžete nasadit společně nebo samostatně jako součást procesu průběžné integrace a nasazování (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Jako je API Management a Logic Apps můžete monitorovat služby Service Bus pomocí Azure monitoru. Platforma Azure Monitor poskytuje informace na základě metrik, které jsou konfigurovány pro každou službu. Azure Monitor je standardně povolená.

## <a name="security"></a>Zabezpečení

Zabezpečení služby Service Bus pomocí SAS. Můžete použít [SAS ověřování](../service-bus-messaging/service-bus-sas.md) udělit přístup uživatelů k prostředkům služby Service Bus se konkrétní práva. Další informace najdete v tématu [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Pokud fronty Service Bus musí být vystavena jako koncový bod HTTP (pro publikování nové zprávy), používejte API Management k zabezpečení podle popředí koncový bod. Koncový bod je pak svázat s certifikáty nebo OAuth podle potřeby. Nejjednodušší způsob, jak zabezpečit koncový bod je s využitím aplikace logiky s triggerem požadavku nebo odpovědi HTTP jako prostředník.

Event Grid zabezpečuje doručování událostí prostřednictvím ověřovacího kódu. Pokud používáte Logic Apps zpracovávat události, ověření se provádí automaticky. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Další postup

- Další informace o [jednoduché podniková integrace](logic-apps-architectures-simple-enterprise-integration.md).