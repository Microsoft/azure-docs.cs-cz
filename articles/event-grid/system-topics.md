---
title: Systémová témata v Azure Event Grid
description: Popisuje systémová témata v Azure Event Grid.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b3a6e7528da2a11c2f91007425ab8beecaf920c3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297279"
---
# <a name="system-topics-in-azure-event-grid"></a>Systémová témata v Azure Event Grid
Systémové téma v Event Grid představuje jednu nebo více událostí publikovaných službami Azure, jako jsou Azure Storage a Azure Event Hubs. Například systémové téma může představovat **všechny události objektů BLOB** nebo pouze objekty **BLOB vytvořené** a **odstraněné objekty blob** publikované pro **konkrétní účet úložiště**. Když se v tomto příkladu nahraje objekt blob do účtu úložiště, služba Azure Storage publikuje událost **vytvořeného objektu BLOB** do systémového tématu v Event Grid, která pak přepošle událost [účastníkům](event-handlers.md) tématu, které obdrží a zpracuje událost. 

> [!NOTE] 
> Pouze služby Azure mohou publikovat události do systémových témat. Proto nezískáte koncovou klávesu ani přístupové klávesy, které můžete použít k publikování událostí, jako je třeba pro vlastní témata nebo domény.

## <a name="azure-services-that-support-system-topics"></a>Služby Azure, které podporují systémová témata
Tady je aktuální seznam služeb Azure, které podporují vytváření systémových témat.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
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

## <a name="system-topics-as-azure-resources"></a>Systémová témata jako prostředky Azure
V minulosti bylo téma v systému implicitní a nebylo zveřejněné pro jednoduchost. Systémová témata se teď zobrazují jako prostředky Azure a poskytují tyto možnosti:

- [Zobrazit systémová témata v Azure Portal](create-view-manage-system-topics.md#view-all-system-topics)
- Exportovat šablony Správce prostředků pro systémová témata a odběry událostí v Azure Portal
- [Nastavení diagnostických protokolů pro systémová témata](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- Nastavení upozornění na chyby publikování a doručení 

## <a name="lifecycle-of-system-topics"></a>Životní cyklus systémových témat
Systémové téma můžete vytvořit dvěma způsoby: 

- Vytvořte [odběr událostí v prostředku Azure jako prostředek rozšíření](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), který automaticky vytvoří systémové téma s názvem ve formátu: `<Azure resource name>-<GUID>` . V případě odstranění posledního odběru událostí pro toto téma je automaticky odstraněno systémové téma vytvořené tímto způsobem. 
- Vytvořte systémové téma pro prostředek Azure a pak vytvořte odběr událostí pro toto systémové téma. Když použijete tuto metodu, můžete zadat název systémového tématu. Když poslední odběr události odstraníte, nebude se toto téma automaticky odstraňovat. Musíte ho odstranit ručně. 

    Při použití Azure Portal je tato metoda vždy používána. Když vytvoříte odběr událostí pomocí [stránky **události** prostředku Azure](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage), nejprve se vytvoří systémové téma a pak se vytvoří odběr tohoto tématu. Můžete explicitně vytvořit systémové téma jako první, a to pomocí [stránky **Event Grid systémových témat** ](create-view-manage-system-topics.md#create-a-system-topic) a pak vytvořit předplatné pro toto téma. 

Když použijete šablonu [CLI](create-view-manage-system-topics-cli.md), [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)nebo [Azure Resource Manager](create-view-manage-system-topics-arm.md), můžete zvolit jednu z výše uvedených metod. Doporučujeme nejprve vytvořit systémové téma a pak vytvořit odběr v tématu, protože se jedná o nejnovější způsob vytváření systémových témat.

Pokud jste nastavili zásady Azure tak, aby ji služba Event Grid nevytvořila, vytvoření tématu v systému se nepodaří. Můžete mít třeba zásadu, která umožňuje vytvořit jenom určité typy prostředků (například: Azure Storage, Azure Event Hubs atd.) v předplatném. 

## <a name="location-and-resource-group-for-a-system-topic"></a>Umístění a skupina prostředků pro systémové téma
V případě zdrojů událostí Azure, které jsou v určité oblasti nebo umístění, je systémové téma vytvořeno ve stejném umístění jako zdroj události Azure. Pokud třeba vytvoříte odběr událostí pro úložiště objektů BLOB v Azure v Východní USA, bude se v systému vytvořit téma v Východní USA. U globálních zdrojů událostí Azure, jako jsou například předplatná Azure, skupiny prostředků nebo Azure Maps, Event Grid vytvoří systémové téma v **globálním** umístění. 

Obecně platí, že je systémové téma vytvořeno ve stejné skupině prostředků, ve které je zdroj události Azure. V případě odběrů událostí vytvořených v oboru předplatného Azure se v **západní USA 2** oblasti vytvoří systémové téma ve výchozí skupině prostředků **EventGrid** . Pokud skupina prostředků neexistuje, Azure Event Grid ji vytvoří před vytvořením systémového tématu. 

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Pomocí Azure Portal vytvářet, zobrazovat a spravovat systémová témata](create-view-manage-system-topics.md).
- [Vytváření, zobrazování a Správa systémových témat Event Grid pomocí Azure CLI](create-view-manage-system-topics-cli.md)
- [Vytváření Event Grid systémových témat pomocí šablon Azure Resource Manager](create-view-manage-system-topics-arm.md)
