---
title: Řešení běžných chyb v Azure Cosmos DB rozhraní API Cassandra
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v Azure Cosmos DB rozhraní API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658567"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Řešení běžných problémů v Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB je vrstva kompatibility, která poskytuje [podporu drátového protokolu](cassandra-support.md) pro oblíbenou Open zdrojovou databázi Apache Cassandra a využívá ji [Azure Cosmos DB](./introduction.md). Jako plně spravovaná cloudová služba Azure Cosmos DB poskytuje [záruky dostupnosti, propustnosti a konzistence](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) rozhraní API Cassandra. Tyto záruky nejsou možné ve starších implementacích Apache Cassandra. Rozhraní API Cassandra také usnadňuje operace s platformami s nulovou údržbou a opravami bez výpadků. V takovém případě se mnohé z back-endové operace liší od Apache Cassandra, takže doporučujeme konkrétní nastavení a postupy, abyste předešli běžným chybám. 

Tento článek popisuje běžné chyby a řešení pro aplikace využívající Azure Cosmos DB rozhraní API Cassandra. Pokud vaše chyba není uvedená níže a při provádění [podporované operace v rozhraní API Cassandra](cassandra-support.md)dojde k chybě, kde při *použití nativního Apache Cassandrau není k dispozici* chyba, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Toto je obálka nejvyšší úrovně s velkým počtem možných příčin a vnitřních výjimek, z nichž mnohé můžou být související s klientem. 
### <a name="solution"></a>Řešení
Některé oblíbené příčiny a řešení jsou následující: 
- Časový limit nečinnosti Azure LoadBalancers: může se také jednat o manifest jako `ClosedConnectionException` . Pokud to chcete vyřešit, nastavte možnost zachovat nastavení v části ovladač (viz [níže](#enable-keep-alive-for-java-driver)), zvyšte nastavení udržování v operačním systému nebo [upravte časový limit nečinnosti v Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Vyčerpání prostředků klientské aplikace:** Ujistěte se, že klientské počítače mají dostatek prostředků k dokončení žádosti. 

## <a name="cannot-connect-to-host"></a>Nejde se připojit k hostiteli.
Může se zobrazit tato chyba: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Řešení
Může to být vyčerpání SNAT na straně klienta. Pro [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) k tomuto problému použijte postup v SNAT. Může to být také problém s nečinným časovým limitem, kdy služba Azure Load Balancer ve výchozím nastavení má 4 minuty časového limitu nečinnosti. V dokumentaci [vypršel časový limit nečinnosti nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). V nastavení ovladače povolte možnost TCP-keep alive (viz [níže](#enable-keep-alive-for-java-driver)) a nastavte `keepAlive` interval v operačním systému na méně než 4 minuty.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
Celkový počet spotřebovaných jednotek žádostí je větší než počet zpracovaných jednotek požadavků v prostoru a v tabulce. Proto jsou požadavky omezené.
### <a name="solution"></a>Řešení
Zvažte možnost škálování propustnosti přiřazené k prostoru klíčů nebo tabulce z Azure Portal (podívejte se [na téma pro](manage-scale-cassandra.md) operace škálování v rozhraní API Cassandra) nebo můžete implementovat zásady opakování. V případě jazyka Java se podívejte na ukázky znovu pro ovladač [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) a [ovladač v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Viz také [rozšíření Azure Cosmos Cassandra pro jazyk Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException i s dostatečnou propustností 
Systém se jeví jako omezování požadavků navzdory dostatečné propustnosti zřízené pro objem požadavků a/nebo spotřebované náklady na jednotku žádosti. Existují dvě možné příčiny neočekávaného omezení četnosti:
- **Operace na úrovni schématu:** Rozhraní API Cassandra implementuje rozpočet systémové propustnosti pro operace na úrovni schématu (CREATE TABLE, ALTER TABLE, DROP TABLE). Tento rozpočet by měl být dostatečný pro operace schématu v produkčním systému. Pokud ale máte vysoký počet operací na úrovni schématu, je možné, že tento limit překročíte. Vzhledem k tomu, že tento rozpočet není řízený uživatelem, bude nutné zvážit snížení počtu spuštěných operací schématu. Pokud se tato akce nepovede vyřešit, nebo ji nemůžete použít pro vaše úlohy, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Zešikmení dat:** při zřizování propustnosti v rozhraní API Cassandra je rozdělen rovnoměrně mezi fyzické oddíly a každý fyzický oddíl má horní limit. Pokud máte vložená nebo dotazovaná data z jednoho konkrétního oddílu, je možné omezit rychlost, i když zřizujete velké množství celkové propustnosti (jednotek požadavků) pro danou tabulku. Zkontrolujte svůj datový model a ujistěte se, že nemáte nadměrné zkosení, které by mohly způsobovat aktivní oddíly. 

## <a name="intermittent-connectivity-errors-java"></a>Chyby přerušovaného připojení (Java) 
Dojde k neočekávanému ukončení připojení nebo vyprší.

### <a name="solution"></a>Řešení 
Ovladače Apache Cassandra pro jazyk Java poskytují dvě zásady nativního opětovného připojení: `ExponentialReconnectionPolicy` a `ConstantReconnectionPolicy` . Výchozí formát je `ExponentialReconnectionPolicy`. Pro Azure Cosmos DB rozhraní API Cassandra však doporučujeme `ConstantReconnectionPolicy` zpoždění 2 sekundy. Podívejte se na [dokumentaci ovladače](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  pro ovladač Java v4. [x a pokyny](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) pro Java 3. x najdete v tématu [Konfigurace ReconnectionPolicy pro příklady ovladačů Java](#configuring-reconnectionpolicy-for-java-driver) níže.

## <a name="error-with-load-balancing-policy"></a>Chyba u zásad vyrovnávání zatížení

Pokud jste implementovali zásady vyrovnávání zatížení v systému V3. x ovladače Java DataStax, s podobným kódem jako v následujícím příkladu:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Pokud hodnota pro `withLocalDc()` neodpovídá datovému centru kontaktních bodů, může docházet k velmi přerušované chybě: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Řešení 
Implementujte [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (možná budete muset upgradovat podverzi DataStax, abyste ji mohli použít):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Počet neúspěšných pro velkou tabulku
Když je spuštěný `select count(*) from table` nebo podobný pro velký počet řádků, vyprší časový limit serveru.

### <a name="solution"></a>Řešení 
Pokud používáte místního klienta CQLSH, můžete se pokusit změnit `--connect-timeout` nastavení nebo `--request-timeout` (další podrobnosti najdete [tady](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Pokud to není dostatečné a počet je stále nedostatečný, můžete získat počet záznamů z telemetrie Azure Cosmos DB back-endu, a to tak, že na kartě metriky v Azure Portal vyberete metriku a `document count` pak přidáte filtr pro databázi nebo kolekci (Analogová hodnota tabulky v Azure Cosmos DB). Potom můžete umístit ukazatel myši na výsledný graf v čase, ve kterém chcete zjistit počet záznamů.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="zobrazení metrik":::


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
