---
title: Co je Azure Event Grid?
description: Odesílejte data událostí ze zdroje obslužným rutinám pomocí Služby Azure Event Grid. Vytvářejte aplikace založené na událostech a integrujte je se službami Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 02/20/2020
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 65fd9d06c8b25e0aff94429f8eb95bb922da330c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77505919"
---
# <a name="what-is-azure-event-grid"></a>Co je Azure Event Grid?

Azure Event Grid umožňuje snadno sestavovat aplikace pomocí architektur založených na událostech. Nejprve vyberte prostředek Azure, ke které se chcete přihlásit, a pak uložte obslužné rutině události nebo koncovému bodu WebHook, na který chcete událost odeslat. Event Grid má integrovanou podporu událostí pocházejících ze služeb Azure, jako jsou služby Storage Blob a skupiny prostředků. Event Grid podporuje také vlastní události s využitím vlastních témat. 

Pomocí filtrů můžete směrovat konkrétní události do různých koncových bodů, zahájit vícesměrové vysílání do více koncových bodů a ujistit se o spolehlivém doručení událostí.

Azure Event Grid se nasadí k maximalizaci dostupnosti nativně rozprostřením mezi více domén selhání v každé oblasti a napříč zónami dostupnosti (v oblastech, které je podporují). Seznam oblastí podporovaných programem Event Grid naleznete v tématu [Produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Tento článek obsahuje přehled Azure Event Gridu. Pokud chcete začít pracovat s Event Gridem, přečtěte si článek [Vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md). 

![Model mřížky událostí zdrojů a obslužných rutin](./media/overview/functional-model.png)

Tento obrázek ukazuje, jak Event Grid spojuje zdroje a obslužné rutiny a není úplný seznam podporovaných integrací.

## <a name="event-sources"></a>Zdroje událostí

Úplné podrobnosti o možnostech jednotlivých zdrojů a související články najdete v tématu věnovaném [zdrojům událostí](event-sources.md). V současné době podporují odesílání událostí do Event Gridu následující služby Azure:

* [Předplatná Azure (operace správy)](event-sources.md#azure-subscriptions)
* [Registr kontejnerů](event-sources.md#container-registry)
* [Vlastní témata](event-sources.md#custom-topics)
* [Centra událostí](event-sources.md#event-hubs)
* [IoT Hub](event-sources.md#iot-hub)
* [Trezor klíčů (náhled)](event-sources.md#key-vault-preview)
* [Media Services](event-sources.md#media-services)
* [Skupiny prostředků (operace správy)](event-sources.md#resource-groups)
* [Service Bus](event-sources.md#service-bus)
* [Objekt blob úložiště](event-sources.md#storage)
* [Azure Maps](event-sources.md#maps)
* [App Configuration](event-sources.md#app-configuration)
* [Signál R](event-sources.md#azure-signalr)
* [Strojové učení](event-sources.md#azure-machine-learning)

## <a name="event-handlers"></a>Obslužné rutiny událostí

Úplné podrobnosti o možnostech jednotlivých obslužných rutin a související články najdete v tématu věnovaném [obslužným rutinám událostí](event-handlers.md). V současné době podporují zpracování událostí z Event Gridu následující služby Azure: 

* [Azure Automation](event-handlers.md#azure-automation)
* [Azure Functions](event-handlers.md#azure-functions)
* [Centra událostí](event-handlers.md#event-hubs)
* [Hybridní připojení](event-handlers.md#hybrid-connections)
* [Logic Apps](event-handlers.md#logic-apps)
* [Power Automate (dříve známý jako Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](event-handlers.md#service-bus)
* [Úložiště fronty](event-handlers.md#queue-storage)
* [Webové háčky](event-handlers.md#webhooks)

## <a name="concepts"></a>Koncepty

Azure Event Grid obsahuje pět konceptů, které vám pomůžou rychle začít s prací:

* **Události** – to, co se stalo.
* **Zdroje událostí** – kde k události došlo.
* **Témata** – koncový bod, kam vydavatelé odesílají události.
* **Odběry událostí** – koncový bod nebo vestavěný mechanismus pro směrování událostí, někdy na více než jednu obslužnou rutinu. Pomocí odběrů taky obslužné rutiny inteligentně filtrují příchozí události.
* **Obslužné rutiny událostí** – aplikace nebo služba reagující na danou událost.

Další informace o těchto konceptech najdete v článku [Koncepty ve službě Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Možnosti

Toto jsou některé klíčové vlastnosti služby Azure Event Grid:

* **Jednoduchost** – kliknutím můžete nasměrovat události prostředku Azure do kterékoli obslužné rutiny události nebo koncového bodu.
* **Rozšířené filtrování** – filtrujte podle typu události nebo cesty k publikování události, abyste měli jistotu, že obslužné rutiny událostí obdrží pouze relevantní události.
* **Fan-out** - Přihlaste se k odběru několika koncových bodů ke stejné události a odešlete kopie události na tolik míst, kolik je potřeba.
* **Spolehlivost** - 24hodinová opakování s exponenciálním vypnutím, abyste se ujistili, že jsou události doručovány.
* **Platby za události** – plaťte jenom za to, do jaké míry Event Grid využíváte.
* **Vysoká propustnost** – sestavujte ve službě Event Grid úlohy s vysokým objemem díky podpoře milionů událostí za sekundu.
* **Integrované události** – integrované události s definovanými prostředky umožňují rychle zahájit práci.
* **Vlastní události** – Pomocí mřížky událostí můžete směrovat, filtrovat a spolehlivě doručovat vlastní události ve vaší aplikaci.

Porovnání služeb Event Grid, Event Hubs a Service Bus najdete v článku [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Co můžu dělat se službou Event Grid?

Azure Event Grid poskytuje několik funkcí, které výrazně zlepšují práci bez serveru, automatizaci operací a [integraci:](https://azure.com/integration) 

### <a name="serverless-application-architectures"></a>Architektury aplikací bez serveru

![Architektura aplikací bez serveru](./media/overview/serverless_web_app.png)

Event Grid propojuje zdroje dat a obslužné rutiny událostí. Pomocí funkce Event Grid můžete například aktivovat funkci bez serveru, která analyzuje image při přidání do kontejneru úložiště objektů blob. 

### <a name="ops-automation"></a>Automatizace operací

![Automatizace operací](./media/overview/Ops_automation.png)

Event Grid umožňuje urychlit automatizaci a zjednodušit vynucování zásad. Pomocí event gridu můžete například upozornit Azure Automation při vytvoření virtuálního počítače nebo databáze SQL. Pomocí událostí můžete automaticky zkontrolovat, zda jsou konfigurace služby kompatibilní, vložit metadata do provozních nástrojů, označit virtuální počítače nebo pracovní položky souborů.

### <a name="application-integration"></a>Integrace aplikací

![Integrace aplikací s Azure](./media/overview/app_integration.png)

Event Grid propojuje vaši aplikaci s dalšími službami. Můžete třeba vytvořit vlastní téma pro odesílání dat událostí aplikace do služby Event Grid a využít tak její spolehlivé doručování, pokročilé směrování a přímou integraci s Azure. Nebo můžete použít Event Grid s Logic Apps ke zpracování dat kdekoli, bez psaní kódu. 

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
  Kurz, ve kterém se služba Azure Functions používá ke streamování dat ze služby Event Hubs do SQL Data Warehouse.
* [Referenční informace k rozhraní REST API služby Event Grid](/rest/api/eventgrid)  
  Poskytuje referenční obsah pro správu odběrů událostí, směrování a filtrování.
