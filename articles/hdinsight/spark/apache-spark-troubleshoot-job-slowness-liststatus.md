---
title: Zpracování úloh streamování Apache Spark ve službě Azure HDInsight trvá déle než obvykle.
description: Zpracování úloh streamování Apache Spark ve službě Azure HDInsight trvá déle než obvykle.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679429"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Scénář: Zpracování úloh streamování Apache Spark ve službě Azure HDInsight trvá déle než obvykle.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Zjistíte, že některé úlohy streamování Apache Spark jsou pomalé nebo čekají na zpracování déle než obvykle. U aplikací pro streamování Sparku Každá dávka zpráv odpovídá jedné úloze odeslané do Sparku. Pokud zpracování úlohy obvykle trvá X sekund, může občas trvat 2-3 minut déle než obvykle.

## <a name="cause"></a>Příčina

Jednou z možných příčin je, že Apache Kafka výrobce trvá déle než 2 minuty, než se dokončí napsání do clusteru Kafka. Chcete-li dále ladit problém Kafka, můžete přidat protokolování do kódu, který používá producenta Kafka k odesílání zpráv a koreluje s protokoly z clusteru Kafka.

Další možnou příčinou je, že časté operace čtení a zápisu do WASB můžou způsobit prodlevu dalších mikrodávkování. Implementace `Filesystem.listStatus` WASB je velmi pomalá kvůli `O(n!)` algoritmu pro odebrání duplicit. Využívá příliš mnoho paměti z důvodu nadbytečného převodu z `BlobListItem` na `FileMetadata` na `FileStatus`. Například algoritmus trvá více než 30 minut, než se zobrazí seznam 700 000 souborů. Takže pokud `ListBlobs` se SparkSQL každou mikrodávku, způsobí to, že při každém mikrodávkování dojde k prodlevě dalších mikrodávkových dávek, a to za následek zpoždění při vysokém plánování. [Tato oprava](https://issues.apache.org/jira/browse/HADOOP-15547) problém vyřeší, ale pokud ve vašem prostředí chybí, `ListBlobs` bude se vám vyskytnout vysoká latence. I když se soubory odstraňují každou hodinu, musí výpis v back-endu iterovat všechny řádky (včetně odstranění), protože proces uvolňování paměti ještě nebyl dokončen. I když by oprava mohla problém vyřešit, může problém uvolňování paměti pořád způsobit zpoždění při zpracování dávek v datových proudech.

## <a name="resolution"></a>Řešení

Použijte opravu [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) . Pokud to není možné, můžete jako umístění kontrolního bodu použít HDFS. Nastavte `checkpointDirectory` na něco podobného `hdfs://mycluster/checkpoint`:.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
