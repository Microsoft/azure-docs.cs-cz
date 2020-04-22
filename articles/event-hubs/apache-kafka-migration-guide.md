---
title: Migrace do Centra událostí Azure pro Apache Kafka
description: Tento článek ukazuje, jak spotřebitelé a výrobci, kteří používají různé protokoly (AMQP, Apache Kafka a HTTPS) můžete vyměňovat události při používání Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677353"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrace do Centra událostí Azure pro ekosystémy Apache Kafka
Azure Event Hubs zveřejňuje koncový bod Apache Kafka, který umožňuje připojení k event hubům pomocí protokolu Kafka. Provedením minimálních změn ve vaší stávající aplikaci Kafka se můžete připojit k Azure Event Hubs a využívat výhod ekosystému Azure. Event Hubs pro Kafka podporují [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html) a novější.

## <a name="pre-migration"></a>Před migrací 

### <a name="create-an-azure-account"></a>Vytvoření účtu Azure
Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.

### <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Postupujte podle podrobných pokynů v článku [Vytvořit centrum událostí](event-hubs-create.md) a vytvořte obor názvů Event Hubs a centrum událostí. 

### <a name="connection-string"></a>Připojovací řetězec
Postupujte podle kroků z [připojovacího řetězce Získat z článku portálu.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) A poznamenejte si připojovací řetězec pro pozdější použití. 

### <a name="fully-qualified-domain-name-fqdn"></a>Plně kvalifikovaný název domény (FQDN)
Můžete také potřebovat hlavní název názvu, který odkazuje na obor názvů centra událostí. Hlavní název ve spojení lze nalézt v připojovacím řetězci následujícím způsobem:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Pokud je obor názvů Event Hubs nasazený v neveřejném cloudu, může \*se název \*vaší domény \*lišit (například .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net nebo .servicebus.cloudapi.de).

## <a name="migration"></a>Migrace 

### <a name="update-your-kafka-client-configuration"></a>Aktualizace konfigurace klienta Kafka

Chcete-li se připojit k centru událostí s podporou Kafka, budete muset aktualizovat konfigurace klienta Kafka. Pokud máte potíže s nalezením vašeho, `bootstrap.servers` zkuste vyhledat, kde je ve vaší aplikaci nastaveno.

Vložte následující konfigurace všude tam, kde to dává smysl ve vaší aplikaci. Nezapomeňte aktualizovat hodnoty `bootstrap.servers` `sasl.jaas.config` a nasměrovat klienta do koncového bodu Kafka centra událostí se správným ověřováním. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Pokud `sasl.jaas.config` není podporovaná konfigurace ve vašem rámci, najít konfigurace, které se používají k nastavení uživatelského jména SASL a heslo a místo nich použít. Nastavte uživatelské jméno `$ConnectionString` a heslo k připojovacímu řetězci Event Hubs.

## <a name="post-migration"></a>Po migraci
Spusťte aplikaci Kafka, která odesílá události do centra událostí. Potom ověřte, že centrum událostí přijímá události pomocí portálu Azure. Na stránce **Přehled** oboru názvů Centra událostí přepněte do zobrazení **Zprávy** v části **Metriky.** Aktualizujte stránku a aktualizujte graf. Může trvat několik sekund, než se zobrazí, že zprávy byly přijaty. 

[![Ověřte, zda centrum událostí přijalo zprávy](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Další kroky
Další informace o centru událostí a centru událostí pro Kafku najdete v následujících článcích:  

- [Průvodce odstraňováním potíží apache Kafka pro centra událostí](apache-kafka-troubleshooting-guide.md)
- [Nejčastější dotazy - Event Hubs for Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Průvodce vývojáři Apache Kafka pro Azure Event Hubs](apache-kafka-developer-guide.md)
- [Doporučené konfigurace](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)