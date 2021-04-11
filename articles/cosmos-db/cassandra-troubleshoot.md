---
title: Řešení běžných chyb v Azure Cosmos DB rozhraní API Cassandra
description: Tento článek popisuje běžné problémy v Azure Cosmos DB rozhraní API Cassandra a jejich řešení.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967349"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Řešení běžných problémů v Azure Cosmos DB rozhraní API Cassandra

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v [Azure Cosmos DB](./introduction.md) je vrstva kompatibility, která poskytuje [podporu drátového protokolu](cassandra-support.md) pro Open Source databázi Apache Cassandra.

Tento článek popisuje běžné chyby a řešení pro aplikace, které používají rozhraní API Cassandra Azure Cosmos DB. Pokud vaše chyba není v seznamu uvedená a při spuštění [podporované operace v Cassandra](cassandra-support.md)dojde k chybě, ale při použití nativního Apache Cassandra se chyba nezobrazí, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Jako plně spravovaná cloudová služba Azure Cosmos DB poskytuje [záruky dostupnosti, propustnosti a konzistence](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) rozhraní API Cassandra. Rozhraní API Cassandra také usnadňuje operace s platformou s nulovou údržbou a opravami bez výpadku.
>
>Tyto záruky nejsou možné v předchozích implementacích Apache Cassandra, takže mnohé z rozhraní API Cassandra back-end operací se liší od Apache Cassandra. Doporučujeme konkrétní nastavení a přístupy, které vám pomůžou se vyhnout běžným chybám.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Tato chyba je obálka nejvyšší úrovně s velkým počtem možných příčin a vnitřních výjimek, z nichž mnoho může být spojeno s klientem.

Běžné příčiny a řešení:

- **Časový limit nečinnosti Azure LoadBalancers**: Tento problém může také být v manifestu `ClosedConnectionException` . Pokud chcete tento problém vyřešit, nastavte v ovladači nastavení Keep-Alive (viz [Povolení udržování otevřených připojení pro ovladač Java](#enable-keep-alive-for-the-java-driver)), zvyšte nastavení udržování otevřených připojení v operačním systému nebo [upravte časový limit nečinnosti v Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Vyčerpání prostředků klientské aplikace**: Ujistěte se, že klientské počítače mají dostatek prostředků k dokončení žádosti.

## <a name="cant-connect-to-a-host"></a>Nejde se připojit k hostiteli.

Může se zobrazit tato chyba: "nelze se připojit k žádnému hostiteli, plánování opakování během 600000 milisekund."

Tato chyba může být způsobena vyčerpáním zdrojového překladu adres (SNAT) na straně klienta. Podle kroků v [SNAT pro odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) vydejte tomuto problému.

Tato chyba může být také příčinou nečinného časového limitu, kdy nástroj pro vyrovnávání zatížení Azure má ve výchozím nastavení čtyři minuty nečinnosti. Viz [časový limit nečinnosti nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Povolte možnost Keep-Alive pro ovladač Java](#enable-keep-alive-for-the-java-driver) a nastavte `keepAlive` interval v operačním systému na méně než čtyři minuty.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Požadavky jsou omezené, protože celkový počet spotřebovaných jednotek žádostí je vyšší než počet jednotek požadavků, které jste zřídili v prostoru klíčů nebo tabulce.

Zvažte možnost škálování propustnosti přiřazené k prostoru klíčů nebo tabulce z Azure Portal (viz [Elastické škálování Azure Cosmos DB rozhraní API Cassandra účtu](manage-scale-cassandra.md)) nebo implementace zásady opakování.

V případě jazyka Java se podívejte na ukázky pro opakování [ovladače v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) a [ovladače v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Viz také [rozšíření Azure Cosmos Cassandra pro jazyk Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException navzdory dostatečné propustnosti

Zdá se, že systém omezuje požadavky, i když je dostatečná propustnost zajištěna pro objem požadavků nebo spotřebované náklady na jednotku žádosti. K dispozici jsou dvě možné příčiny:

- **Operace na úrovni schématu**: rozhraní API Cassandra implementuje rozpočet systémové propustnosti pro operace na úrovni schématu (Create Table, ALTER TABLE, drop Table). Tento rozpočet by měl být dostatečný pro operace schématu v produkčním systému. Pokud ale máte vysoký počet operací na úrovni schématu, možná tento limit překročíte.

  Vzhledem k tomu, že rozpočet není řízen uživatelem, zvažte snížení počtu operací se schématem, které spustíte. Pokud tato akce problém nevyřeší nebo to není možné pro vaše zatížení, [vytvořte žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- Předimenzování **dat**: Pokud je v rozhraní API Cassandra zajištěna propustnost, je rovnoměrně rozdělena mezi fyzické oddíly a každý fyzický oddíl má horní limit. Pokud máte v jednom konkrétním oddílu vložená nebo dotazovaná data s vysokým objemem dat, může to být omezené i v případě, že pro tuto tabulku zřídíte velké množství celkové propustnosti (jednotek požadavků).

  Zkontrolujte svůj datový model a ujistěte se, že nemáte nadměrné zkosení, které by mohly způsobit aktivní oddíly.

## <a name="intermittent-connectivity-errors-java"></a>Chyby přerušovaného připojení (Java)

Dojde k neočekávanému ukončení připojení nebo vyprší.

Ovladače Apache Cassandra pro jazyk Java poskytují dvě zásady nativního opětovného připojení: `ExponentialReconnectionPolicy` a `ConstantReconnectionPolicy` . Výchozí formát je `ExponentialReconnectionPolicy`. Pro Azure Cosmos DB rozhraní API Cassandra však doporučujeme `ConstantReconnectionPolicy` zpoždění dvou sekund.

Příklady naleznete v [dokumentaci k ovladači Java 4. x](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), [dokumentaci k ovladači Java 3. x](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)nebo [konfiguraci ReconnectionPolicy pro příklady ovladačů Java](#configure-reconnectionpolicy-for-the-java-driver) .

## <a name="error-with-load-balancing-policy"></a>Chyba u zásad vyrovnávání zatížení

Možná jste implementovali zásady vyrovnávání zatížení ve verzi v3. x ovladače Java DataStax s podobným kódem:

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

Pokud hodnota neodpovídá `withLocalDc()` datovému centru kontaktních bodů, může dojít k občasné chybě: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Implementujte rozhraní [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Aby to fungovalo, možná budete muset upgradovat DataStax pomocí následujícího kódu:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Počet se v rozsáhlé tabulce nezdařil.

Když spouštíte `select count(*) from table` nebo se jedná o velký počet řádků, vyprší časový limit serveru.

Pokud používáte místního klienta CQLSH, změňte `--connect-timeout` `--request-timeout` nastavení nebo. Viz [cqlsh: prostředí CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Pokud počet stále vyprší, můžete získat počet záznamů z Azure Cosmos DB telemetrie back-endu tak, že přejdete na kartu metriky v Azure Portal, vyberete metriku `document count` a poté přidáte filtr pro databázi nebo kolekci (Analogová hodnota tabulky v Azure Cosmos DB). Potom můžete umístit ukazatel myši na výsledný graf v čase, ve kterém chcete zjistit počet záznamů.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="zobrazení metrik":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Konfigurace ReconnectionPolicy pro ovladač Java

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

## <a name="enable-keep-alive-for-the-java-driver"></a>Povolit možnost Keep-Alive pro ovladač Java

### <a name="version-3x"></a>Verze 3. x

V případě verze 3. x ovladače Java nastavte možnost Keep-Alive při vytváření objektu clusteru a pak zajistěte, aby [v operačním systému byla povolená](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)možnost Keep-Alive:

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

V případě verze 4. x ovladače Java nastavte možnost Keep-Alive přepsáním nastavení v `reference.conf` nástroji a potom zkontrolujte, jestli je [v operačním systému povolená](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)možnost Keep-Alive:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [podporovaných funkcích](cassandra-support.md) v rozhraní API Cassandra Azure Cosmos DB.
- Naučte se [migrovat z nativního Apache Cassandra na Azure Cosmos DB rozhraní API Cassandra](cassandra-migrate-cosmos-db-databricks.md).
