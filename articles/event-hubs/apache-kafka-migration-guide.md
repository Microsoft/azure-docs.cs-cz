---
title: Migrace na Azure Event Hubs pro Apache Kafka
description: Tento článek vysvětluje, jak migrovat klienty z Apache Kafka do Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8db14f6ec18843bf7d592d56b3cf2352c3901a9a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310032"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrace na službu Azure Event Hubs pro ekosystémy Apache Kafka
Azure Event Hubs zpřístupňuje koncový bod Apache Kafka, který umožňuje připojení k Event Hubs pomocí protokolu Kafka. Díky minimálním změnám v existující aplikaci Kafka se můžete připojit k Azure Event Hubs a těžit výhody ekosystému Azure. Event Hubs funguje s mnoha vašimi stávajícími aplikacemi Kafka, včetně nástroje MirrorMaker. Další informace najdete v tématu [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="pre-migration"></a>Před migrací 

### <a name="create-an-azure-account"></a>Vytvoření účtu Azure
Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

### <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
Pokud chcete vytvořit obor názvů Event Hubs a centrum událostí, postupujte podle podrobných pokynů v článku [vytvoření centra událostí](event-hubs-create.md) . 

### <a name="connection-string"></a>Připojovací řetězec
Postupujte podle kroků v článku [získání připojovacího řetězce z portálu](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . A poznamenejte si připojovací řetězec pro pozdější použití. 

### <a name="fully-qualified-domain-name-fqdn"></a>Plně kvalifikovaný název domény (FQDN)
Možná budete potřebovat plně kvalifikovaný název domény, který odkazuje na obor názvů centra událostí. Plně kvalifikovaný název domény najdete v rámci připojovacího řetězce následujícím způsobem:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Pokud je váš obor názvů Event Hubs nasazený v neveřejném cloudu, může se název domény lišit (například \* . ServiceBus.chinacloudapi.cn, \* . ServiceBus.usgovcloudapi.NET nebo \* . ServiceBus.cloudapi.de).

## <a name="migration"></a>Migrace 

### <a name="update-your-kafka-client-configuration"></a>Aktualizace konfigurace klienta Kafka

Pokud se chcete připojit k centru událostí s povoleným Kafka, budete muset aktualizovat konfiguraci klientů Kafka. Pokud se vám nedaří najít svoji svoji práci, zkuste vyhledat, kde `bootstrap.servers` je ve vaší aplikaci nastavená.

Vložte následující konfigurace všude, kde je to ve vaší aplikaci smysl. Nezapomeňte aktualizovat `bootstrap.servers` hodnoty a a `sasl.jaas.config` nasměrovat klienta na Event Hubs koncový bod Kafka se správným ověřováním. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Pokud `sasl.jaas.config` není ve vašem rozhraní podporovaná konfigurace, najděte konfigurace, které se používají k nastavení uživatelského jména a hesla SASL, a použijte je místo toho. Nastavte uživatelské jméno `$ConnectionString` a heslo k vašemu Event Hubs připojovacímu řetězci.

## <a name="post-migration"></a>Po migraci
Spusťte aplikaci Kafka, která odesílá události do centra událostí. Pak ověřte, že centrum událostí přijímá události pomocí Azure Portal. Na stránce **Přehled** oboru názvů Event Hubs přepněte do zobrazení **zprávy** v části **metriky** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy. 

[![Ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs a Event Hubs pro Kafka najdete v následujících článcích:  

- [Průvodce odstraňováním potíží s Apache Kafka pro Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Nejčastější dotazy – Event Hubs pro Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)
- [Doporučené konfigurace](apache-kafka-configurations.md)