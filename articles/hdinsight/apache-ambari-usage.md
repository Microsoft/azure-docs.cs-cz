---
title: Použití Apache Ambari v Azure HDInsight
description: Diskuze o tom, jak se používá Apache Ambari v Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 6233c42d7fbf8dc7821d26f77171c44485fb8d34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946939"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Použití Apache Ambari v Azure HDInsight

HDInsight používá Apache Ambari k nasazování a správě clusteru. Ambari agenti běží na všech uzlech (hlavnímu uzlu, pracovní uzel, Zookeeper a edgenode, pokud existuje). Ambari server běží pouze na hlavnímu uzlu (hn0 nebo HN1). V jednom okamžiku se spustí jenom jedna instance serveru Ambari. Tuto kontrolu řídí kontroler převzetí služeb při selhání HDInsight. Pokud je jeden z hlavních pro restartování nebo údržbu vypnutý, ostatní hlavnímu uzlu se stanou aktivními a na druhé hlavnímu uzlu se spustí server Ambari.

Veškerá konfigurace clusteru by se měla provádět prostřednictvím [uživatelského rozhraní Ambari](./hdinsight-hadoop-manage-ambari.md), při restartování uzlu se přepíší všechny místní změny.

## <a name="failover-controller-services"></a>Služby řadiče pro převzetí služeb při selhání

Kontroler převzetí služeb při selhání HDInsight zodpovídá taky za aktualizaci IP adresy hostitele hlavnímu uzlu, která odkazuje na aktuální aktivní hlavní uzel. Všichni agenti Ambari jsou nakonfigurováni tak, aby nahlásily svůj stav a prezenční signál do hostitele hlavnímu uzlu. Kontroler převzetí služeb při selhání je sada služeb spuštěných na všech uzlech v clusteru, pokud nejsou spuštěné, hlavnímu uzlu převzetí služeb při selhání nemusí správně fungovat a při pokusu o přístup k serveru Ambari se zobrazí HTTP 502.

Pokud chcete zjistit, která hlavnímu uzlu je aktivní, jedním ze způsobů, jak se jednat o jeden z uzlů v clusteru, `ping headnodehost` a pak spusťte a porovnejte IP adresu se dvěma hlavních.

Pokud služby řadiče pro převzetí služeb při selhání neběží, hlavnímu uzlu převzetí služeb při selhání nemusí správně fungovat, což může mít za problém, že server Ambari nebude spuštěn Pokud chcete zjistit, jestli jsou spuštěné služby kontroleru převzetí služeb při selhání, spusťte:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Protokoly

V aktivním hlavnímu uzlu můžete kontrolovat protokoly serveru Ambari na adrese:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Na jakémkoli uzlu v clusteru můžete ověřit protokoly agenta Ambari na adrese:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sekvence spuštění služby

Toto je pořadí spuštění služby při spuštění:

1. HDInsight-agent spouští služby řadiče pro převzetí služeb při selhání.
1. Služby kontroleru převzetí služeb při selhání spouštějí agenta Ambari na všech uzlech a na Ambari serveru na aktivním hlavnímu uzlu.

## <a name="ambari-database"></a>Databáze Ambari

HDInsight vytvoří databázi v SQL Database pod kapotou, aby sloužila jako databáze pro server Ambari. Výchozí [úroveň služby je S0](../azure-sql/database/elastic-pool-scale.md).

V případě jakéhokoli clusteru s počtem pracovních uzlů, který je větší než 16 při vytváření clusteru, je S2 vrstva služby databáze.

## <a name="takeaway-points"></a>Body poznatkem

Nikdy ručně nespouštějte nebo zastavte služby Ambari-Server nebo Ambari-agent, pokud se nepokoušíte restartovat službu, abyste mohli problém vyřešit. Pokud chcete vynutit převzetí služeb při selhání, můžete restartovat aktivní hlavnímu uzlu.

Nikdy ručně Neměňte žádné konfigurační soubory na žádném uzlu clusteru, takže Ambari uživatelské rozhraní provede úlohu.

## <a name="property-values-in-esp-clusters"></a>Hodnoty vlastností v clusterech ESP

Ve službě HDInsight 4,0 Balíček zabezpečení podniku clustery používejte `|` jako oddělovače proměnných kanály místo čárky. Příklad najdete níže:

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Správa clusterů HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
