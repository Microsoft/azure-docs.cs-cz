---
title: Ladění operací se souborem WASB v Azure HDInsight
description: Popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470713"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Ladění operací se souborem WASB v Azure HDInsight

Jsou chvíle, kdy můžete chtít pochopit, jaké operace ovladač WASB začal s Azure Storage. Na straně klienta vytvoří ovladač WASB protokoly pro každou operaci systému souborů na úrovni **LADĚNÍ.** Ovladač WASB používá log4j k řízení úrovně protokolování a výchozí je **úroveň INFO.** Protokoly analýz na straně serveru Azure Storage najdete v tématu [protokolování analýzy Azure Storage](../../storage/common/storage-analytics-logging.md).

Produkovaný protokol bude vypadat podobně jako:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Zapnutí protokolu ladění WASB pro operace se soubory

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`přejděte `CLUSTERNAME` na , kde je název clusteru Spark.

1. Přejděte na **rozšířené vlastnosti spark2-log4j - .**

    1. Změnit `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`na .

    1. Přidat `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Přejděte na **Rozšířené livy2-log4j-vlastnosti**.

    Přidat `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Uložit změny.

## <a name="additional-logging"></a>Dodatečné protokolování

Výše uvedené protokoly by měly poskytovat vysoké úrovně pochopení operací systému souborů. Pokud výše uvedené protokoly stále neposkytují užitečné informace nebo pokud chcete prozkoumat volání rozhraní API úložiště objektů blob, přidejte `fs.azure.storage.client.logging=true` do . `core-site` Toto nastavení povolí protokoly java sdk pro ovladač úložiště WASB a vytiskne každé volání na server úložiště objektů blob. Odeberte nastavení po prošetření, protože by mohlo rychle zaplnit disk a může zpomalit proces.

Pokud je back-end založen na Azure Data Lake, použijte následující nastavení log4j pro komponentu (například spark/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Vyhledejte protokoly `/var/log/adl/adl.log` v protokolech.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
