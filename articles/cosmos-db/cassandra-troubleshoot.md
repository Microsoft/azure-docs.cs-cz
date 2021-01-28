---
title: Řešení běžných chyb v Azure Cosmos DB rozhraní API Cassandra
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v Azure Cosmos DB rozhraní API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 75ce307056d24f84e9f72f746eb77b09b89e21b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927499"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Řešení běžných problémů v Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB je vrstva kompatibility, která poskytuje [podporu drátového protokolu](cassandra-support.md) pro oblíbenou Open zdrojovou databázi Apache Cassandra a využívá ji [Azure Cosmos DB](./introduction.md). Jako plně spravovaná cloudová služba Azure Cosmos DB poskytuje [záruky dostupnosti, propustnosti a konzistence](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) rozhraní API Cassandra. Tyto záruky nejsou možné ve starších implementacích Apache Cassandra. Rozhraní API Cassandra také usnadňuje operace s platformami s nulovou údržbou a opravami bez výpadků. V takovém případě se mnohé z back-endové operace liší od Apache Cassandra, takže doporučujeme konkrétní nastavení a postupy, abyste předešli běžným chybám. 

Tento článek popisuje běžné chyby a řešení pro aplikace využívající Azure Cosmos DB rozhraní API Cassandra.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               |  Popis             | Řešení  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | Celkový počet spotřebovaných jednotek žádostí je větší než počet zpracovaných jednotek požadavků v prostoru a v tabulce. Proto jsou požadavky omezené. | Zvažte možnost škálování propustnosti přiřazené k prostoru klíčů nebo tabulce z Azure Portal (podívejte se [na téma pro](manage-scale-cassandra.md) operace škálování v rozhraní API Cassandra) nebo můžete implementovat zásady opakování. V případě jazyka Java se podívejte na ukázky znovu pro ovladač [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) a [ovladač v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Viz také [rozšíření Azure Cosmos Cassandra pro jazyk Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| OverloadedException (Java) i s dostatečnou propustností | Systém se jeví jako omezování požadavků navzdory dostatečné propustnosti zřízené pro objem požadavků a/nebo spotřebované náklady na jednotku žádosti.  | Rozhraní API Cassandra implementuje rozpočet systémové propustnosti pro operace na úrovni schématu (CREATE TABLE, ALTER TABLE, DROP TABLE). Tento rozpočet by měl být dostatečný pro operace schématu v produkčním systému. Pokud ale máte vysoký počet operací na úrovni schématu, je možné, že tento limit překročíte. Vzhledem k tomu, že tento rozpočet není řízený uživatelem, bude nutné zvážit snížení počtu spuštěných operací schématu. Pokud se tato akce nepovede vyřešit, nebo ji nemůžete použít pro vaše úlohy, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Po uplynutí doby nečinnosti po úspěšném připojení se aplikace nemůže připojit.| K této chybě mohlo dojít z důvodu vypršení časového limitu nečinnosti Azure LoadBalancers, což je 4 minuty. V části ovladač nastavte nastavení Keep Alive (viz níže) a zvyšte nastavení udržování v operačním systému nebo [upravte časový limit nečinnosti v Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Další chyby přerušovaného připojení (Java) | Neočekávaně dojde k poklesu nebo vypršení časového limitu | Ovladače Apache Cassandra pro jazyk Java poskytují dvě zásady nativního opětovného připojení: `ExponentialReconnectionPolicy` a `ConstantReconnectionPolicy` . Výchozí formát je `ExponentialReconnectionPolicy`. Pro Azure Cosmos DB rozhraní API Cassandra však doporučujeme `ConstantReconnectionPolicy` zpoždění 2 sekundy. Podívejte se na [dokumentaci ovladače](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  pro ovladač Java v4. x a [návod pro jazyk](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) Java 3. x (viz také příklady níže).|

Pokud vaše chyba není uvedená výše a při provádění [podporované operace v rozhraní API Cassandra](cassandra-support.md)dojde k chybě, kde při *použití nativního Apache Cassandra* chyba není k dispozici, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) .

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Konfigurace ReconnectionPolicy pro ovladač Java

### <a name="version-3x"></a>Verze 3. x

Pro verzi 3. x ovladače Java nakonfigurujte zásady opětovného připojení při vytváření objektu clusteru:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Verze 4. x

V případě verze 4. x ovladače Java nakonfigurujte zásady opětovného připojení přepsáním nastavení v `reference.conf` souboru:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Povolit možnost Keep-Alive pro ovladač Java

### <a name="version-3x"></a>Verze 3. x

V případě verze 3. x ovladače Java nastavte možnost Keep-Alive při vytváření objektu clusteru a ujistěte se, [že je v operačním systému povolená](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)možnost Keep-Alive:

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Verze 4. x

V případě verze 4. x ovladače Java nastavte možnost Keep-Alive přepsáním nastavení v `reference.conf` a zajistěte, aby byl [v operačním systému povolený](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)režim Keep-Alive:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [podporovaných funkcích](cassandra-support.md) v Azure Cosmos DB rozhraní API Cassandra.
- Naučte se [migrovat z nativního Apache Cassandra na Azure Cosmos DB rozhraní API Cassandra](cassandra-migrate-cosmos-db-databricks.md)