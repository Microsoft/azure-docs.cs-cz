---
title: Dynamické přidávání oddílů do centra událostí v Azure Event Hubs
description: V tomto článku se dozvíte, jak dynamicky přidávat oddíly do centra událostí v Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625261"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamické přidávání oddílů do centra událostí (Apache Kafka téma) v Azure Event Hubs
Služba Event Hubs poskytuje datový proud zpráv pomocí schématu rozdělujícího datový proud na oddíly pro jednotlivé příjemce. To zajišťuje, aby každý příjemce četl jenom konkrétní podmnožinu nebo oddíl datového proudu zpráv. Toto schéma umožňuje vodorovné škálování zpracování událostí a poskytuje další funkce zaměřené na datový proud, které nejsou ve frontách a tématech k dispozici. Oddíl je seřazená posloupnost událostí, která se nachází v centru událostí. Jakmile přijdete o novější události, přidají se na konec této sekvence. Další informace o oddílech obecně najdete v [oddílu oddíly](event-hubs-scalability.md#partitions) .

V době vytváření centra událostí můžete zadat počet oddílů. V některých scénářích možná budete muset po vytvoření centra událostí přidat oddíly. Tento článek popisuje, jak dynamicky přidávat oddíly do existujícího centra událostí. 

> [!IMPORTANT]
> Dynamické přidávání oddílů je k dispozici pouze na **vyhrazených** Event Hubs clusterech.

> [!NOTE]
> V případě klientů Apache Kafka se **centrum událostí** mapuje na **téma Kafka**. Další mapování mezi Azure Event Hubs a Apache Kafka najdete v tématu [koncepční mapování Kafka a Event Hubs](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Aktualizace počtu oddílů
V této části se dozvíte, jak aktualizovat počet oddílů centra událostí různými způsoby (PowerShell, CLI atd.).

### <a name="powershell"></a>PowerShell
K aktualizaci oddílů v centru událostí použijte příkaz prostředí PowerShell [set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) . 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>Rozhraní příkazového řádku
[`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update)K aktualizaci oddílů v centru událostí použijte příkaz CLI. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Šablona Resource Manageru
Aktualizujte hodnotu `partitionCount` vlastnosti v šabloně správce prostředků a znovu nasaďte šablonu, aby se provedená aktualizace prostředku. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
`AlterTopics`K zvýšení počtu oddílů použijte rozhraní API (například prostřednictvím nástroje **Kafka-témata** CLI). Podrobnosti najdete v tématu [Úprava Kafka témat](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Event Hubs klienti
Pojďme se podívat, jak Event Hubs se klienti chovají, když se počet oddílů v centru událostí aktualizuje. 

Když přidáte oddíl do existujícího sudého centra, klient centra událostí obdrží `MessagingException` od služby informace o tom, že se v nich změnila metadata entit (entita je vaše centrum událostí a metadata jsou informace o oddílu). Klienti automaticky znovu otevřou odkazy AMQP, které by následně vybraly změněné informace metadat. Klienti pak pracují normálně.

### <a name="senderproducer-clients"></a>Klienti pro odesílatele/producenta
Event Hubs poskytuje tři možnosti odesílatele:

- **Dělit odesílatel** – v tomto scénáři klienti odesílají události přímo do oddílu. I když lze oddíly identifikovat a odesílat do nich události přímo, nedoporučujeme tento model. Přidání oddílů nemá vliv na tento scénář. Doporučujeme restartovat aplikace, aby bylo možné detekovat nově přidané oddíly. 
- **Klíč – odesílatel oddílu** – v tomto scénáři klienti odesílají události pomocí klíče, aby všechny události patřící k danému klíči byly ukončeny ve stejném oddílu. V tomto případě služba vyhodnotí klíč a trasy k odpovídajícímu oddílu. Aktualizace počtu oddílů může kvůli změně hodnoty hash způsobovat problémy mimo pořadí. Pokud se tedy zajímáte o řazení, ujistěte se, že aplikace před zvýšením počtu oddílů spotřebovává všechny události z existujících oddílů.
- **Odesílatel kruhového dotazování (výchozí)** – v tomto scénáři robins služba Event Hubs události napříč oddíly a také používá algoritmus vyrovnávání zatížení. Služba Event Hubs ví o změnách počtu oddílů a během několika sekund se pošle na nové oddíly, které mění počet oddílů.

### <a name="receiverconsumer-clients"></a>Klienti pro přijímače/příjemce
Event Hubs poskytuje přímým přijímačům a snadnou uživatelskou knihovnu nazvanou [hostitel procesoru událostí (stará sada SDK)](event-hubs-event-processor-host.md)  nebo [procesor událostí (nová sada SDK)](event-processor-balance-partition-load.md).

- **Přímí příjemci** – přímí příjemci naslouchat konkrétním oddílům. Jejich chování za běhu není ovlivněno při horizontálním navýšení kapacity oddílů pro centrum událostí. Aplikace, která používá přímé přijímače, musí dbát na to, aby vybrala nové oddíly a přiřadila příjemce odpovídajícím způsobem.
- **Procesor událostí hostitel** – tento klient automaticky neaktualizuje metadata entit. Proto by nebylo možné vyhodnotit zvýšení počtu oddílů. Opětovné vytvoření instance procesoru událostí způsobí načtení metadat entity, které zase vytvoří nové objekty blob pro nově přidané oddíly. Již existující objekty blob nebudou ovlivněny. Restartování všech instancí procesoru událostí se doporučuje, aby se zajistilo, že všechny instance budou znát nově přidané oddíly, a vyrovnávání zatížení se mezi spotřebiteli zpracovává správně.

    Pokud používáte starou verzi sady .NET SDK ([windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)), hostitel procesoru událostí při restartování odstraní existující kontrolní bod, pokud se počet oddílů v kontrolním bodu neshoduje s počtem oddílů načteného ze služby. Toto chování může mít vliv na vaši aplikaci. 

## <a name="apache-kafka-clients"></a>Apache Kafka klienti
Tato část popisuje, jak Apache Kafka klienti, kteří používají koncový bod Kafka Azure Event Hubs, se chovají při aktualizaci počtu oddílů v centru událostí. 

Kafka klienti, kteří používají Event Hubs s protokolem Apache Kafka se chovají jinak než klienti centra událostí, kteří používají protokol AMQP. Kafka klienti aktualizují svá metadata jednou za `metadata.max.age.ms` milisekund. Tuto hodnotu zadáte v konfiguracích klienta. `librdkafka`Knihovny používají také stejnou konfiguraci. Aktualizace metadat informují klienty o změnách služby, včetně zvýšení počtu oddílů. Seznam konfigurací najdete v tématu [konfigurace Apache Kafka pro Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Klienti pro odesílatele/producenta
Výrobci vždy určí, že požadavky na odeslání obsahují cíl oddílu pro každou sadu vytvořených záznamů. To znamená, že všechny vytváření oddílů se provádí na straně klienta se zobrazením metadat zprostředkovatele od výrobce. Až budou nové oddíly přidány do zobrazení metadat producenta, budou k dispozici pro žádosti producenta.

### <a name="consumerreceiver-clients"></a>Klienti příjemce/příjemce
Když člen skupiny uživatelů provede obnovení metadat a vybere nově vytvořené oddíly, tento člen zahájí vyrovnání skupiny. Metadata uživatelů se pak aktualizují pro všechny členy skupiny a nové oddíly se přiřadí pomocí přiděleného vedoucího nového vyvážení.

## <a name="recommendations"></a>Doporučení

- Pokud pro své aplikace producenta použijete klíč oddílu a závisí na hodnotě hash klíče k zajištění řazení v oddílu, nedoporučuje se dynamicky přidávat oddíly. 

    > [!IMPORTANT]
    > I když existující data zachovají řazení, bude pro zprávy po změně počtu oddílů v důsledku přidání oddílů přerušeno hashování oddílů.
- Přidání oddílu do existujícího tématu nebo instance centra událostí se doporučuje v následujících případech:
    - Když použijete výchozí metodu odesílání událostí
     - Kafka výchozí strategie dělení na oddíly, příklad – strategie pro rychlé přiřazení


## <a name="next-steps"></a>Další kroky
Další informace o oddílech najdete v tématu [oddíly](event-hubs-scalability.md#partitions).

