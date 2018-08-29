---
title: Enterprise integration architektura vzor – integrační služby Azure
description: Tato referenční architektura ukazuje, jak můžete implementovat vzor podnikové integrace s Azure Logic Apps, Azure API Management, Azure Service Bus a Azure Event Grid
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 2ffb1f7edef0cf92cbbf7adc4314967858bcfeb1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128639"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Architektura integrace Enterprise s frontami a události

Tento článek popisuje architekturu podnikové integrace, který používá osvědčených postupů, které lze použít k integraci aplikace při používání integrace služby Azure. Tato architektura může použít jako základ pro mnoho různých aplikačních vzory, které vyžadují rozhraní API HTTP, pracovních postupů a Orchestrace.

![Diagram architektury – podniková integrace pomocí front a události](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

Tato série popisuje opakovaně použitelné součásti, které může použít k sestavení aplikace obecný integrace. Protože integrace technologie má mnoho možných aplikací, od jednoduchého typu point-to-point aplikace do úplné podnikové aplikace Azure Service Bus, vezměte v úvahu komponenty, které je nutné implementovat pro vaše aplikace a infrastrukturu.

## <a name="architecture-components"></a>Komponenty architektury

Tato architektura vychází architekturu popsanou v článku [referenční architektura: jednoduché podniková integrace](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). Tuto architekturu [doporučení](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) budou použita i zde, ale pro zkrácení, tento článek vynechá z těchto doporučení [doporučení](#recommendations) oddílu. Tato architektura integrace enterprise zahrnuje tyto komponenty:

- **Skupina prostředků**: A [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) je logický kontejner prostředků Azure.

- **Azure API Management**: [API Management](https://docs.microsoft.com/azure/api-management/) service je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní HTTP API.

- **Portál Azure API Management Developer**: Každá instance Azure API Management poskytuje přístup k [portál pro vývojáře](../api-management/api-management-customize-styles.md). Tento portál poskytuje přístup k dokumentaci a ukázky kódu. Můžete také otestovat rozhraní API na portálu pro vývojáře.

- **Služba Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) je platforma pro vytváření pracovních postupů enterprise a integrace bez serveru.

- **Konektory**: Logic Apps používá [konektory](../connectors/apis-list.md) pro připojení k nejčastěji používané služby. Logic Apps nabízí stovky konektorů, ale můžete také vytvořit vlastní konektor.

- **Azure Service Bus**: [služby Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) poskytuje zabezpečené a spolehlivé zasílání zpráv. Můžete použít pro zasílání zpráv pro oddělení aplikací a integraci s jinými systémy založené na zprávách.

- **Azure Event Grid**: [služby Event Grid](../event-grid/overview.md) je platforma pro publikování a doručování událostí aplikace bez serveru.

- **IP adresa**: Služba Azure API Management má pevnou veřejnou [IP adresu](../virtual-network/virtual-network-ip-addresses-overview-arm.md) a názvu domény. Výchozí název domény je subdoména azure-api.net, například contoso.azure-api.net, ale můžete taky nakonfigurovat [vlastních domén](../api-management/configure-custom-domain.md). Logic Apps a služby Service Bus taky mít veřejnou IP adresu. Pro zabezpečení, ale tato architektura omezuje přístup pro volání koncových bodů aplikace logiky na IP adresu služby API Management. Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu (SAS).

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS. Azure DNS poskytuje překlad názvů s využitím infrastruktury Microsoft Azure. Hostovat v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturační účely ostatních služeb Azure. Pokud chcete používat vlastní název domény, třeba contoso.com, vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v tématu [konfigurace vlastního názvu domény ve službě API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: můžete použít [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k koncových bodů rozhraní API pomocí předání [webového tokenu JSON pro službu API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) k ověření. Úrovně Standard a Premium Azure AD můžete zabezpečit přístup k portálu pro vývojáře rozhraní API Management.

## <a name="patterns"></a>Vzory 

Tato architektura používá některé vzory, které jsou základem pro operaci:

* Stávající back endové rozhraní API HTTP publikují prostřednictvím portálu pro vývojáře rozhraní API Management. Na portálu pro vývojáře, kteří jsou buď interně ve vaší organizaci, externí nebo obojí  
volání těchto rozhraní API můžete integrovat do aplikací.

* Složený rozhraní API jsou sestaveny pomocí logic apps, které Orchestrace volání software jako služba (SaaS) systémů, služeb Azure a libovolné rozhraní API, které jsou publikovány do API managementu. Aplikace logiky se také [publikované prostřednictvím portálu pro vývojáře rozhraní API Management](../api-management/import-logic-app-as-api.md).

* Aplikace použít Azure AD pro [získání tokenu zabezpečení OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) , který je potřeba k získání přístupu k rozhraní API.

* Azure API Management [ověří token zabezpečení](../api-management/api-management-howto-protect-backend-with-aad.md) a pak předá požadavek back endové rozhraní API nebo logiku aplikace.

* Fronty Azure Service Bus se používají pro [oddělení](../service-bus-messaging/service-bus-messaging-overview.md) aktivita aplikace a pro [vyhlazení špiček v zatížení](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Zprávy se přidá do fronty v logic apps, aplikace třetích stran, nebo (není vidět) a publikujte do fronty v rozhraní API HTTP prostřednictvím služby API Management.

* Při přidání zprávy do fronty služby Service Bus, aktivuje událost. Událost se aktivuje aplikace logiky, která pak zprávy zpracuje.

* Dalšími službami Azure, jako je Azure Blob Storage a Azure Event Hubs, také publikování událostí do služby Event Grid. Tyto služby spouštění logiky aplikace přijímat události a pak proveďte následující akce.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře, která je popsána v tomto článku. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="service-bus-tier"></a>Úroveň služby Service Bus

Použijte na úrovni služby Service Bus úrovně Premium, která podporuje oznámení služby Event Grid. Další informace najdete v tématu [cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Cenách služby Event Grid

Event Grid používá model bez serveru. Fakturace se vypočítá podle počet operací (provádění události). Další informace najdete v tématu [ceny služby Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). V současné době nejsou k dispozici žádné úrovně důležité informace týkající se služby Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Pomocí PeekLock využívat zprávy služby Service Bus

Když vytvoříte aplikaci logiky, která využívat zprávy služby Service Bus, jste svou aplikaci logiky použít [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) pro přístup k skupinu zpráv. Při použití PeekLock aplikace logiky může provést kroky k ověření každou zprávu před dokončením nebo opuštění. Tento přístup chrání před ztrátou náhodného zprávy.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Vyhledat více objektů, když se aktivuje trigger Event Grid

Když se aktivuje trigger Event Grid, tato akce znamená jednoduše, že "splnit aspoň jednu z těchto věcí ke vzniku." Předpokládejme například, když Event Grid aktivuje aplikace logiky na zprávu, která se zobrazí ve frontě služby Service Bus, aplikace logiky by měla vždy, může být jeden nebo více zpráv, které jsou k dispozici pro zpracování.

### <a name="region"></a>Oblast

Kvůli minimalizaci latence sítě, vyberte stejnou oblast pro API Management a Logic Apps, Service Bus. Obecně platí vyberte oblast co nejblíže vašim uživatelům.

Skupina prostředků má také oblast. Tuto oblast určuje, kam se ukládají metadata nasazení a kde provést nasazení šablony. Chcete-li zlepšit dostupnost během nasazení, umístěte skupinu prostředků a prostředky ve stejné oblasti.

## <a name="scalability"></a>Škálovatelnost

K dosažení vyšší škálovatelnost, na úrovni služby Service Bus Premium můžete horizontálně navýšit kapacitu počtu jednotek zasílání zpráv. Konfigurace úrovně Premium může mít jednu, dvě nebo čtyři jednotky zasílání zpráv. Další informace o škálování služby Service Bus, najdete v části [osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Dostupnost

* U úrovní Basic, Standard a Premium smlouvu o úrovni služeb (SLA) pro službu Azure API Management je aktuálně 99,9 %. Smlouva SLA pro konfigurace úrovně premium s nasazením, který má alespoň jednu jednotku ve dvou nebo více oblastech, dostupnost 99,95 %.

* Smlouva SLA pro Azure Logic Apps je aktuálně 99,9 %.

### <a name="disaster-recovery"></a>Zotavení po havárii

Pokud chcete povolit převzetí služeb při selhání, pokud dojde k závažné výpadku, zvažte implementaci geografického zotavení v Service Bus úrovně Premium. Další informace najdete v tématu [geografického zotavení Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Možnosti správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Samostatné skupiny prostředků usnadňují správu nasazení, odstranění nasazení v testovacím a přiřadit přístupová práva.

Při přiřazování prostředků skupinám prostředků zvažte tyto faktory:

* **Životní cyklus**: cyklem umístěte prostředky, které mají stejný životní cyklus ve stejné skupině prostředků.

* **Přístup**: Chcete-li použít zásady přístupu pro prostředky ve skupině, můžete použít [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md).

* **Fakturace**: můžete zobrazit souhrnné náklady pro skupinu prostředků.

* **Cenová úroveň pro službu API Management**: použití vrstvy pro vývojáře pro vývojová a testovací prostředí. Minimalizovat náklady během předprodukční kolekci, nasadit repliku produkčního prostředí, spuštění testů a poté vypněte.

Další informace naleznete v tématu [Přehled Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Nasazení

* Chcete-li nasazení API managementu, Logic Apps, služby Event Grid a Service Bus, použijte [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). Šablony usnadňují automatizace nasazení pomocí Powershellu nebo rozhraní příkazového řádku Azure.

* API Management, všechny individuální logic apps, tématy Event gridu a obory názvů služby Service Bus do své vlastní samostatné šablony Resource Manageru. Když použijete samostatné šablony, můžete ukládat prostředky v systémy správy zdrojového kódu. Tyto šablony můžete nasadit společně nebo samostatně jako součást procesu průběžné integrace a nasazování (CI/CD).

## <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Jako je API Management a Logic Apps můžete monitorovat služby Service Bus pomocí Azure monitoru, který je ve výchozím nastavení povolené. Platforma Azure Monitor poskytuje informace na základě metrik, které jsou konfigurovány pro každou službu. 

## <a name="security"></a>Zabezpečení

Pro zabezpečení služby Service Bus pomocí sdíleného přístupového podpisu (SAS). Například můžete udělit přístup uživatelů k prostředkům služby Service Bus se konkrétní práva pomocí [SAS ověřování](../service-bus-messaging/service-bus-sas.md). Další informace najdete v tématu [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Pokud je potřeba vystavit frontu služby Service Bus jako koncový bod HTTP, třeba k publikování nové zprávy, API Management můžete použijte k zabezpečení fronty podle popředí koncový bod. Můžete pak zabezpečit koncový bod s certifikáty nebo ověřování OAuth podle potřeby. Nejjednodušší způsob je zabezpečit koncový bod používá aplikace logiky s triggerem požadavku nebo odpovědi HTTP jako prostředník.

Služba Event Grid zabezpečuje doručování událostí prostřednictvím ověřovacího kódu. Pokud používáte Logic Apps zpracovávat události, ověření se provádí automaticky. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Další postup

* Další informace o [jednoduché podniková integrace](logic-apps-architectures-simple-enterprise-integration.md)
