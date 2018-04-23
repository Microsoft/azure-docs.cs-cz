---
title: Co je Azure Event Hubs a proč tuto službu používat | Microsoft Docs
description: Přehled a úvod do služby Azure Event Hubs – ingestování telemetrických údajů z webů, aplikací a zařízení v cloudovém měřítku
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: 08b78f59dc758f12556f332546ccb6043bef2812
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-event-hubs"></a>Co je služba Event Hubs?

Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Díky [možnosti publikování a odebírání dat](/biztalk/core/publish-and-subscribe-architecture) s nízkou latencí a v masivním měřítku slouží služba Event Hubs jako vstupní brána k velkým objemům dat.

Služba Event Hubs například umožňuje sledování chování v mobilních aplikacích, informace o datových přenosech z webových farem, zachycení událostí v hrách na konzolách nebo shromažďování telemetrických údajů z průmyslových strojů, připojených vozidel či jiných zařízení.

## <a name="azure-event-hubs-overview"></a>Přehled služby Azure Event Hubs

V architekturách řešení hraje služba Event Hubs běžně roli „předních dveří“ pro kanál událostí, který se často nazývá *přijímač událostí*. Přijímač událostí je komponenta nebo služba, která se nachází mezi zdroji událostí a příjemci událostí a slouží k oddělení produkce datového proudu událostí od spotřeby těchto událostí. Následující obrázek znázorňuje tuto architekturu:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Služba Event Hubs poskytuje možnost zpracovávat datové proudy zpráv. Její charakteristiky ji ale odlišují od tradičních podnikových způsobů zasílání zpráv. Možnosti služby Event Hubs jsou vycházejí ze scénářů zpracování událostí a vysoké propustnosti. V důsledku toho se služba Event Hubs liší od zasílání zpráv služby [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) a neimplementuje některé možnosti dostupné pro entity [zasílání zpráv Service Bus](/azure/service-bus-messaging/), jako jsou třeba témata.

## <a name="event-hubs-features"></a>Funkce Event Hubs

Event Hubs obsahuje následující klíčové prvky:

- [**Producenti/zdroje událostí:**](event-hubs-features.md#event-publishers) Entita, která odesílá data do centra událostí. Událost se publikuje prostřednictvím AMQP 1.0 nebo HTTPS.
- [**Zachytávání:**](event-hubs-features.md#capture) Umožňuje zachytit streamovaná data Event Hubs a uložit je v účtu Azure Blob Storage.
- [**Oddíly:**](event-hubs-features.md#partitions) Umožňuje každému příjemci načíst jenom konkrétní podmnožinu, nebo oddíl datového proudu událostí.
- [**Tokeny SAS:**](event-hubs-features.md#sas-tokens) Identifikuje a ověřuje zdroj událostí.
- [**Příjemci událostí:**](event-hubs-features.md#event-consumers) Entita, která čte data událostí z centra událostí. Příjemci událostí se připojují prostřednictvím protokolu AMQP 1.0. 
- [**Skupiny příjemců:**](event-hubs-features.md#consumer-groups) Poskytuje každé aplikaci s několika konzumacemi samostatné zobrazení datového proudu událostí a umožňuje, aby tito příjemci jednali nezávisle.
- [**Jednotky propustnosti:**](event-hubs-features.md#capacity) Předem zakoupené jednotky kapacity. Škálování jednoho oddílu dovoluje maximálně jednu jednotku propustnosti.

Technické podrobnosti o těchto a dalších funkcích služby Event Hubs najdete v [přehledu funkcí Event Hubs](event-hubs-features.md). 

## <a name="next-steps"></a>Další kroky

Podrobné informace o cenách služby Event Hubs najdete na stránce [Ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

