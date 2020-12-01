---
title: Co je nového Poznámky k verzi – Azure Event Grid
description: Přečtěte si, co je nového v Azure Event Grid, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 7528cef7007704bbf434f16622707da8920eec2b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350003"
---
# <a name="whats-new-in-azure-event-grid"></a>Co je nového v Azure Event Grid?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` do čtečky informačního kanálu ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Azure Event Grid průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Přidání podpory k novému všeobecně dostupnému rozhraní API služby (GA) verze 2020-06-01.
- Nové funkce, které se staly GA:
    - [Mapování vstupu](input-mappings.md)
    - [Vlastní schéma vstupu](input-mappings.md)
    - [Schéma Cloud Event v10 za účelem](cloud-event-schema.md)
    - [Service Bus téma jako cíl](handler-service-bus.md)
    - [Funkce Azure jako cíl](handler-functions.md)
    - [Dávkování webhooků](./edge/delivery-output-batching.md)
    - [Zabezpečený Webhook (podpora Azure Active Directory)](secure-webhook-delivery.md)
    - [Filtrování IP adres](configure-firewall.md)
    - [Podpora služby privátního propojení](configure-private-endpoints.md)
    - [Schéma doručení událostí](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2 – Preview (2020-05)
- Tato verze zahrnuje další opravy chyb, které zvyšují kvalitu.
- Verze 5.3.1 – Preview odpovídá verzi rozhraní API 2020-04-01-Preview, která zahrnuje následující nové funkce: 
    - [Podpora filtrování IP adres při publikování událostí do domén a témat](configure-firewall.md)
    - [Témata pro partnery](./partner-events-overview.md)
    - [Systémová témata jako sledované prostředky v Azure Portal](system-topics.md)
    - [Doručování událostí s identitou spravované služby](managed-service-identity.md) 
    - [Podpora služby privátního propojení](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1 – Preview (2020-04)
- Tato verze zahrnuje různé opravy chyb, které zvyšují kvalitu.
- Verze 5.3.0 verze Preview odpovídá verzi rozhraní API 2020-04-01-Preview, která zahrnuje následující nové funkce: 
    - [Podpora filtrování IP adres při publikování událostí do domén a témat](configure-firewall.md)
    - [Témata pro partnery](./partner-events-overview.md)
    - [Systémová témata jako sledované prostředky v Azure Portal](system-topics.md)
    - [Doručování událostí s identitou spravované služby](managed-service-identity.md) 
    - [Podpora služby privátního propojení](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-Preview (2020-03)
- Přinášíme nové funkce nad funkcemi již přidaných ve verzi 5.2.0-Preview. 
- Verze 5.2.0 verze Preview odpovídá verzi rozhraní API 2020-04-01-Preview.
- Přidává podporu k těmto novým funkcím: 
    - [Podpora filtrování IP adres při publikování událostí do domén a témat](configure-firewall.md)
    - [Témata pro partnery](./partner-events-overview.md)
    - [Systémová témata jako sledované prostředky v Azure Portal](system-topics.md)
    - [Doručování událostí s identitou spravované služby](managed-service-identity.md) 
    - [Podpora služby privátního propojení](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-Preview (2020-01)
- Tato verze odpovídá verzi rozhraní API 2020-04-01-Preview.
- Přidává podporu k těmto novým funkcím:
    - [Podpora filtrování IP adres při publikování událostí do domén a témat](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Tato verze odpovídá `2019-06-01` verzi rozhraní API.
- Přidává podporu k těmto novým funkcím:
    * [Domény](event-domains.md)
    * Stránkování a vyhledávací filtr pro operace se seznamem prostředků Příklad najdete v tématu [témata – seznam podle předplatného](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Service Bus fronta jako cíl](handler-service-bus.md)
    * [Rozšířené filtrování](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-Preview (2019-03)
- Tato verze odpovídá verzi rozhraní API 2019-02-01-Preview.
- Přidává podporu k těmto novým funkcím:
    * Stránkování a vyhledávací filtr pro operace se seznamem prostředků Příklad najdete v tématu [témata – seznam podle předplatného](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Témata týkající se ručního vytvoření/odstranění domény](how-to-event-domains.md)
    * [Service Bus fronta jako cíl](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Tato verze odpovídá `2019-01-01` stabilní verzi rozhraní API.
- Podporuje obecnou dostupnost (GA) následujících funkcí souvisejících s odběry událostí:
    * [Cíl nedoručeného dopisu](manage-event-delivery.md)
    * [Azure Storage fronta jako cíl](handler-storage-queues.md)
    * [Azure Relay – hybridní připojení jako cíl](handler-relay-hybrid-connections.md)
    * [Ruční ověření metodou handshake](webhook-event-delivery.md)
    * [Podpora pro zásady opakování](delivery-and-retry.md)
- Funkce, které jsou stále ve verzi Preview (například [Event Grid domény](event-domains.md) nebo [Rozšířené filtry](event-filtering.md#advanced-filtering) , jsou stále k dispozici pomocí verze 3.0.1-Preview sady SDK. "

## <a name="301-preview-2018-10"></a>3.0.1-Preview (2018-10)
- Vybírá se závislost na [verzi 10.0.3 balíčku NuGet Newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-Preview (2018-10)
- Tato verze je ukázková sada SDK pro nové funkce, které jsou představené ve verzi 2018-09-15-Preview rozhraní API. – Tato verze zahrnuje podporu pro:
    - [Témata domény a domény](event-domains.md)
    - Představujeme [Datum vypršení platnosti pro odběr událostí](concepts.md#event-subscription-expiration)
    - Představení [pokročilého filtrování](event-filtering.md#advanced-filtering) pro odběry událostí
    - Stabilní verze sady SDK, která cílí na `2018-01-01` verzi rozhraní API, bude dál existovat jako 1.3.0 verze.

## <a name="next-steps"></a>Další kroky