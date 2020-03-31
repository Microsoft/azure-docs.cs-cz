---
title: Osvědčené postupy pro správu clusterů – Azure HDInsight
description: Seznamte se s doporučenými postupy pro správu clusterů HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782018"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Osvědčené postupy pro správu clusteru HDInsight

Seznamte se s doporučenými postupy pro správu clusterů HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Jak vytvořím clustery HDInsight?

| Možnost | Dokumenty |
|---|---|
| Azure Data Factory | [Vytváření clusterů Apache Hadoop na vyžádání ve službě HDInsight pomocí Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Šablona Správce vlastních prostředků | [Vytváření clusterů Apache Hadoop v HDInsightpomocí šablon Správce prostředků](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Šablony Rychlý start | [Šablony rychlých startů HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| ukázek Azure | [Ukázky HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| portál Azure | [Vytváření clusterů založených na Linuxu ve službě HDInsight pomocí portálu Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Vytváření clusterů HDInsight pomocí rozhraní příkazového příkazu Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Vytváření clusterů založených na Linuxu ve HDInsightu pomocí Azure PowerShellu](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Vytvoření clusterů Apache Hadoop pomocí rozhraní Azure REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Sady SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Přejít](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Pokud vytváříte cluster a znovu používáte název clusteru z dříve vytvořeného clusteru, počkejte před vytvořením clusteru na dokončení předchozího odstranění clusteru.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Jak lze přizpůsobit clustery HDInsight?

| Možnost | Dokumenty |
|---|---|
| Akce skriptu | [Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Přizpůsobení clusterů HDInsight pomocí Bootstrapu](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externí metaobchody | [Použití externích úložišť metadat v Azure HDInsightu](./hdinsight-use-external-metadata-stores.md) |
| Vlastní Ambari DB | [Nastavení clusterů HDInsight pomocí vlastní databáze Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Jaké chyby mohou čelit při vytváření clusterů?

| Chyba | Další informace |
|---|---|
| Žádná kvóta | Existují kvóty pro počet kvót, které můžete vytvořit v rámci předplatného v jednotlivých oblastech. Další informace naleznete [v tématu Plánování kapacity: kvóty](./hdinsight-capacity-planning.md). |
| Nejsou k dispozici žádné další IP adresy | Každá virtuální síť má omezený počet IP adres. Při vytváření clusteru HDInsight, každý uzel (včetně zookeeper a uzly brány) používá některé z těchto přidělených IP adres. Při použití všech adres IP dojde k této chybě.  |
| Pravidla skupiny zabezpečení sítě (NSG) neumožňují komunikaci s poskytovateli prostředků HDInsight | Pokud používáte skupiny zabezpečení sítě nebo uživatelem definované trasy (UDR) k řízení příchozího provozu do clusteru HDInsight, musíte zajistit, aby váš cluster mohl komunikovat s kritickými službami stavu a správy Azure. Další informace najdete v tématu [značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight.](./hdinsight-service-tags.md) |
| Opakované použití názvu clusteru | Při použití názvu clusteru, který jste používali dříve, je třeba počkat X počet minut před opětovným vytvořením clusteru. V opačném případě se zobrazí zpráva, že prostředek již existuje. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Jak můžu spravovat spuštěné clustery HDInsight?

| Možnost | Dokumenty |
|---|---|
| Automatické škálování | [Automatické škálování clusterů Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Ruční škálování | [Škálování clusterů Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitorování s Ambari| [Sledování výkonu clusteru v Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitorování pomocí protokolů Azure Monitoru | [Monitorování clusterů HDInsight s využitím protokolů služby Azure Monitor](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Jak zkontroluji odstraněné clustery HDInsight?

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Následující dotaz můžete použít pomocí protokolů Azure Monitor ke sledování odstraněných clusterů.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Další kroky

* [Plánování kapacity pro clustery HDInsight](./hdinsight-capacity-planning.md)
* [Jaké jsou výchozí a doporučené konfigurace uzlů pro Azure HDInsight?](./hdinsight-supported-node-configuration.md)