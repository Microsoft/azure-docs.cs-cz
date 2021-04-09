---
title: Řešení potíží s Event Grid
description: Tento článek popisuje různé způsoby řešení potíží s Azure Event Grid.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720555"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Řešení potíží s Azure Event Grid
Tento článek poskytuje informace, které vám pomůžou při řešení potíží s Azure Event Grid problémy. 

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Povolit nastavení diagnostiky pro Event Grid témata nebo domény pro zachycení a zobrazení protokolů selhání publikování a doručení. Další informace najdete v tématu [diagnostické protokoly](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Metriky
Můžete zobrazit metriky pro Event Grid témata a předplatná a vytvořit na nich výstrahy. Další informace najdete v tématu [Event Grid metriky](monitor-event-delivery.md).

## <a name="alerts"></a>Výstrahy
Vytvořte výstrahy na Azure Event Grid metriky a operace protokolu aktivit. Další informace najdete v tématu [výstrahy týkající se Event Grid metrik a protokolů aktivit](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problémy ověření předplatného
Při vytváření odběru událostí se může zobrazit chybová zpráva s oznámením, že se nepovedlo ověřit zadaný koncový bod. Řešení potíží s ověřováním předplatného najdete v tématu [řešení potíží s Event Grid odběry](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problémy s připojením k síti
K dispozici jsou různé důvody pro klientské aplikace, které se nemohou připojit k Event Gridmu tématu nebo doméně. Problémy s připojením mohou být trvalé nebo přechodné. Informace o tom, jak tyto problémy vyřešit, najdete v tématu řešení potíží s [připojením](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Kódy chyb
Pokud se zobrazí chybové zprávy s kódy chyb, například 400, 409 a 403, přečtěte si téma [řešení potíží s Event Gridmi](troubleshoot-errors.md)chybami. 

## <a name="distributed-tracing-net"></a>Distribuované trasování (.NET)
Knihovna Event Grid .NET podporuje trasování distribuce. Aby bylo možné dodržovat [pokyny specifikace CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) pro distribuci trasování, knihovna nastaví `traceparent` a `tracestate` na [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) v případě, že `CloudEvent` je povoleno distribuované trasování. Další informace o tom, jak povolit distribuované trasování ve vaší aplikaci, najdete v [dokumentaci k distribuovanému trasování](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)sady Azure SDK.

### <a name="sample"></a>Ukázka
Podívejte se na [ukázku čítače řádku](/samples/azure/azure-sdk-for-net/line-counter/). Tato ukázková aplikace znázorňuje použití klientů úložiště, Event Hubs a Event Grid spolu s ASP.NET Core integrací, distribuovaným trasováním a hostovanými službami. Umožňuje uživatelům odeslat soubor do objektu blob, který spustí událost Event Hubs obsahující název souboru. Procesor Event Hubs přijme událost a aplikace stáhne objekt BLOB a spočítá počet řádků v souboru. Aplikace zobrazí odkaz na stránku, která obsahuje počet řádků. Po kliknutí na odkaz bude CloudEvent obsahující název souboru publikován pomocí Event Grid.

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc, vystavte svůj problém ve [fóru Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) nebo otevřete [lístek podpory](https://azure.microsoft.com/support/options/). 
