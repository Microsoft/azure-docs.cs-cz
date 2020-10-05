---
title: Co je Azure Event Grid?
description: Odešle data události ze zdroje do obslužných rutin pomocí Azure Event Grid. Sestavujte aplikace založené na událostech a integrujte je se službami Azure.
ms.topic: overview
ms.date: 09/24/2020
ms.openlocfilehash: 73d218058c5f95d8c2d2324f1a0e1e77b24f1364
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326490"
---
# <a name="what-is-azure-event-grid"></a>Co je Azure Event Grid?

Azure Event Grid umožňuje snadno sestavovat aplikace pomocí architektur založených na událostech. Nejdřív vyberte prostředek Azure, ke kterému se chcete přihlásit, a pak udělte obslužné rutině události nebo koncovému bodu Webhooku událost, která se má odeslat. Event Grid má integrovanou podporu událostí pocházejících ze služeb Azure, jako jsou služby Storage Blob a skupiny prostředků. Event Grid podporuje také vlastní události s využitím vlastních témat. 

Filtry můžete použít ke směrování konkrétních událostí do různých koncových bodů, k vícesměrovému vysílání do více koncových bodů a k zajišťování spolehlivého doručení událostí.

Azure Event Grid je nasazená, aby se maximalizovala dostupnost nativně rozprostřená napříč několika doménami selhání v každé oblasti a mezi zónami dostupnosti (v oblastech, které je podporují). Seznam oblastí, které jsou podporovány nástrojem Event Grid, najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Tento článek obsahuje přehled Azure Event Gridu. Pokud chcete začít pracovat s Event Gridem, přečtěte si článek [Vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Event Grid model zdrojů a obslužných rutin" lightbox="./media/overview/functional-model.png":::

Tento obrázek ukazuje, jak Event Grid propojuje zdroje a obslužné rutiny a není vyčerpávajícím seznamem podporovaných integrací.

## <a name="event-sources"></a>Zdroje událostí

V současné době následující služby Azure podporují odesílání událostí do Event Grid. Chcete-li získat další informace o zdroji v seznamu, vyberte odkaz.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Skupiny prostředků Azure](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Předplatná Azure](event-schema-subscriptions.md)

## <a name="event-handlers"></a>Obslužné rutiny událostí

Úplné podrobnosti o možnostech jednotlivých obslužných rutin a související články najdete v tématu věnovaném [obslužným rutinám událostí](event-handlers.md). V současné době podporují zpracování událostí z Event Gridu následující služby Azure: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Hybrid Connections přenosu](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Automatizace (dříve označované jako Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [Webhooky](handler-webhooks.md)

## <a name="concepts"></a>Koncepty

Azure Event Grid obsahuje pět konceptů, které vám pomůžou rychle začít s prací:

* **Události** – to, co se stalo.
* **Zdroje událostí** – kde k události došlo.
* **Témata** – koncový bod, kam vydavatelé odesílají události.
* **Odběry událostí** – koncový bod nebo integrovaný mechanismus pro směrování událostí, někdy do více než jedné obslužné rutiny. Pomocí odběrů taky obslužné rutiny inteligentně filtrují příchozí události.
* **Obslužné rutiny událostí** – aplikace nebo služba reagující na danou událost.

Další informace o těchto konceptech najdete v článku [Koncepty ve službě Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Možnosti

Toto jsou některé klíčové vlastnosti služby Azure Event Grid:

* **Jednoduchost** – kliknutím můžete nasměrovat události prostředku Azure do kterékoli obslužné rutiny události nebo koncového bodu.
* **Rozšířené filtrování** – filtrovat podle typu události nebo cesty publikování události, aby obslužné rutiny událostí přijímaly pouze relevantní události.
* **Ventilátor** – Přihlaste se k odběru několika koncových bodů na stejnou událost, která odešle kopie události na tolik míst podle potřeby.
* **Spolehlivost** – 24 hodin opakování pomocí exponenciálního omezení rychlostiu, aby se zajistilo doručení událostí.
* **Platby za události** – plaťte jenom za to, do jaké míry Event Grid využíváte.
* **Vysoká propustnost** – sestavujte ve službě Event Grid úlohy s vysokým objemem díky podpoře milionů událostí za sekundu.
* **Integrované události** – integrované události s definovanými prostředky umožňují rychle zahájit práci.
* **Vlastní události** – pomocí Event Grid můžete směrovat, filtrovat a spolehlivě doručovat vlastní události ve vaší aplikaci.

Porovnání služeb Event Grid, Event Hubs a Service Bus najdete v článku [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Co můžu dělat se službou Event Grid?

Azure Event Grid poskytuje několik funkcí, které výrazně zlepšují práci bez serveru, automatizaci operací a [integraci](https://azure.com/integration) : 

### <a name="serverless-application-architectures"></a>Architektury aplikací bez serveru

![Architektura aplikace bez serveru](./media/overview/serverless_web_app.png)

Event Grid propojuje zdroje dat a obslužné rutiny událostí. Pomocí Event Grid můžete například aktivovat funkci bez serveru, která analyzuje obrázky při přidání do kontejneru úložiště objektů BLOB. 

### <a name="ops-automation"></a>Automatizace operací

![Automatizace operací](./media/overview/Ops_automation.png)

Event Grid umožňuje urychlit automatizaci a zjednodušit vynucování zásad. Můžete například použít Event Grid k upozorňování Azure Automation, když se vytvoří virtuální počítač nebo databáze v Azure SQL. Pomocí událostí můžete automaticky ověřit, jestli konfigurace služby splňují předpisy, vkládat metadata do operací Operations Tools, označit virtuální počítače nebo pracovní položky souboru.

### <a name="application-integration"></a>Integrace aplikací

![Integrace aplikací s Azure](./media/overview/app_integration.png)

Event Grid propojuje vaši aplikaci s dalšími službami. Můžete třeba vytvořit vlastní téma pro odesílání dat událostí aplikace do služby Event Grid a využít tak její spolehlivé doručování, pokročilé směrování a přímou integraci s Azure. Nebo můžete použít Event Grid s Logic Apps ke zpracování dat kdekoli bez psaní kódu. 

## <a name="how-much-does-event-grid-cost"></a>Kolik stojí Event Grid?

Azure Event Grid využívá model plateb za události, takže platíte jenom za to, co využijete. Prvních 100 000 operací za měsíc je zdarma. Operace jsou definované jako příchozí události, pokusy o doručení, volání správy a filtrování podle přípony předmětu. Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Další kroky

* [Směrování událostí objektů blob úložiště](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Pomocí služby Event Grid můžete reagovat na události objektů blob úložiště.
* [Vytváření vlastních událostí a přihlášení k jejich odběru](custom-event-quickstart.md)  
  Pusťte se rovnou do práce a pomocí rychlého startu pro Azure Event Grid začněte odesílat vlastní události do libovolného koncového bodu.
* [Použití Logic Apps jako obslužné rutiny událostí](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Kurz týkající se sestavení aplikace pomocí Logic Apps, která bude reagovat na události sdílené službou Event Grid.
* [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)  
  Kurz, který používá Azure Functions ke streamování dat z Event Hubs do Azure synapse Analytics.
* [Referenční informace k rozhraní REST API služby Event Grid](/rest/api/eventgrid)  
  Poskytuje referenční obsah pro správu odběrů, směrování a filtrování událostí.