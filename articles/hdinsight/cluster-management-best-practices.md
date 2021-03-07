---
title: Osvědčené postupy pro správu clusteru – Azure HDInsight
description: Seznamte se s osvědčenými postupy pro správu clusterů HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7ab113a9c7ab03a477f387c5599b55d67c99c6fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433892"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Osvědčené postupy pro správu clusteru HDInsight

Seznamte se s osvědčenými postupy pro správu clusterů HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Návody vytvářet clustery HDInsight?

| Možnost | dokumenty. |
|---|---|
| Azure Data Factory | [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Šablona vlastního Správce prostředků | [Vytváření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Šablony pro rychlý start | [Šablony pro rychlý Start HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| ukázek Azure | [Ukázky HDInsight Azure](/samples/browse/?products=azure-hdinsight) |
| portál Azure | [Vytváření clusterů se systémem Linux v HDInsight pomocí Azure Portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Vytváření clusterů HDInsight pomocí rozhraní příkazového řádku Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Vytváření clusterů se systémem Linux v HDInsight pomocí Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Vytváření clusterů Apache Hadoop pomocí Azure REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Sady SDK (.NET, Python, Java) | [.NET](/dotnet/api/overview/azure/hdinsight), [Python](/python/api/overview/azure/hdinsight?preserve-view=true&view=azure-python), [Java](/java/api/overview/azure/hdinsight), [Přejít](./hdinsight-go-sdk-overview.md) |

> [!Note]
> Pokud vytváříte cluster a znovu použijete název clusteru z dříve vytvořeného clusteru, před vytvořením clusteru počkejte, než se dokončí předchozí odstranění clusteru.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Návody Přizpůsobení clusterů HDInsight?

| Možnost | dokumenty. |
|---|---|
| Akce skriptů | [Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md) |
| Metoda bootstrap | [Přizpůsobení clusterů HDInsight pomocí Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externí metaúložiště | [Použití externích úložišť metadat v Azure HDInsightu](./hdinsight-use-external-metadata-stores.md) |
| Vlastní Ambari DB | [Nastavení clusterů HDInsight s vlastní Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Jaké jsou některé chyby, na které se mám setkat při vytváření clusterů?

| Chyba | Další informace |
|---|---|
| Žádná kvóta | Existují kvóty pro počet jader, které můžete v rámci předplatného vytvořit v každé oblasti. Další informace najdete v tématu [plánování kapacity: kvóty](./hdinsight-capacity-planning.md). |
| Nejsou k dispozici žádné další IP adresy. | Každá virtuální síť má omezený počet IP adres. Při vytváření clusteru HDInsight každý uzel (včetně uzlů Zookeeper a bran) používá některé z těchto přidělených IP adres. V případě, že se používají všechny IP adresy, dojde k této chybě.  |
| Pravidla skupiny zabezpečení sítě (NSG) neumožňují komunikaci s poskytovateli prostředků HDInsight. | Pokud k řízení příchozího provozu do clusteru HDInsight používáte skupin zabezpečení sítě nebo uživatelsky definované trasy (udr), musíte zajistit, aby váš cluster mohl komunikovat s důležitými službami stavu a správy Azure. Další informace najdete v tématu [značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight](./hdinsight-service-tags.md) . |
| Opětovné použití názvu clusteru | Pokud použijete název clusteru, který jste použili dříve, je nutné před opětovným vytvořením clusteru počkat X počet minut. V opačném případě se zobrazí zpráva, že prostředek již existuje. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Návody spravovat spuštěné clustery HDInsight?

| Možnost | dokumenty. |
|---|---|
| Automatické škálování | [Automatické škálování clusterů Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Ruční škálování | [Škálování clusterů Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitorování pomocí Ambari| [Monitorování výkonu clusteru v Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitorování pomocí protokolů Azure Monitor | [Monitorování clusterů HDInsight s využitím protokolů služby Azure Monitor](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Problémy se službami, plánovaná údržba, & poradce zabezpečení pro stav | [Přihlášení k odběru výstrah stavu služby pro konkrétní předplatné](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Návody kontrolovat odstraněné clustery HDInsight?

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

K monitorování odstraněných clusterů můžete použít následující dotaz s protokoly Azure Monitor.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Další kroky

* [Plánování kapacity pro clustery HDInsight](./hdinsight-capacity-planning.md)
* [Jaké jsou výchozí a doporučené konfigurace uzlů pro Azure HDInsight?](./hdinsight-supported-node-configuration.md)
