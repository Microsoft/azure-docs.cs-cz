---
title: Ukázky – služba Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje seznam ukázky pro službu Azure Event Hubs najdete na Githubu.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7231b6a33c94dfe029f41f034c7674b386090d7a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104046"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Úložiště Git s ukázkami pro Azure Event Hubs 
Ukázky služby Event Hubs můžete najít na [Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples). Tyto ukázky ukazují klíčové funkce v [Azure Event Hubs](/azure/event-hubs/). Tento článek slouží ke kategorizaci a popisuje ukázek dostupných s odkazy na každý.

## <a name="net-samples"></a>Ukázky .NET

| Ukázkový název | Popis | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Tento příklad ukazuje, jak napsat konzolovou aplikaci .NET Core, který odesílá události do centra událostí. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Tento příklad ukazuje, jak psát aplikace konzoly .NET Core, která přijímá sadu událostí z centra událostí pomocí knihovny Event Processor Host.  | 

## <a name="java-samples"></a>Ukázky Java

| Ukázkový název | Popis | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Tento příklad ukazuje, jak ingestování dávky události do vašeho centra událostí. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Tento příklad ukazuje, jak pro zpracování příjmu událostí do centra událostí. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Tento příklad ukazuje různé možnosti k dispozici s Event Hubs k ingestování událostí. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Tento příklad ukazuje, jak přijímat události z oddílu centra událostí pomocí konkrétní posun data a času. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Tento příklad ukazuje, jak přijímat události z oddílu centra událostí pomocí specifických dat posun. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Tento příklad ukazuje, jak můžou přijímat z oddílů centra událostí pomocí pořadové číslo. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Tento příklad ukazuje, jak přijímat události z centra událostí pomocí třídy event processor host, který poskytuje automatické oddílu výběru a převzetí služeb při selhání mezi několik příjemců souběžných. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Tento příklad ukazuje, jak v Centru událostí automaticky vertikálně navýšit kapacitu na vysoké zatížení. Ukázka bude odesílat události s rychlostí, které právě překračují nakonfigurované frekvence centra událostí, způsobí vertikálně navýšit kapacitu centra událostí. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Tato ukázka umožňuje měřit rychlost příchozího přenosu dat. | 

## <a name="python-samples"></a>Ukázky Pythonu
Můžete najít ukázky Pythonu pro Azure Event Hubs [azure event hubs python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) úložiště GitHub.

## <a name="nodejs-samples"></a>Ukázky Node.js
Můžete najít ukázky Node.js pro Azure Event Hubs [azure event hubs uzly](https://github.com/Azure/azure-event-hubs-node) úložiště GitHub.

## <a name="go-samples"></a>Ukázky v go
Můžete najít ukázky Go pro Azure Event Hubs v [azure event hubs průběžně](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) úložiště GitHub.

## <a name="azure-cli-samples"></a>Ukázky Azure CLI
Můžete najít ukázky v Azure CLI pro službu Azure Event Hubs v [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) úložiště GitHub.

## <a name="azure-powershell-samples"></a>Ukázky Azure PowerShellu
Můžete najít ukázky Azure Powershellu pro Azure Event Hubs [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) úložiště GitHub.
 
## <a name="apache-kafka-samples"></a>Apache Kafka ukázky
Můžete najít ukázky pro službu Event Hubs pro Apache Kafka funkci [azure-event-hubs pro kafka](https://github.com/Azure/azure-event-hubs-for-kafka) úložiště GitHub.

## <a name="next-steps"></a>Další postup
Dozvíte víc o službě Event Hubs v následujících článcích:

- [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
- [Funkce Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)