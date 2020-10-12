---
title: Ladění operací se soubory WASB ve službě Azure HDInsight
description: Popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77470713"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Ladění operací se soubory WASB ve službě Azure HDInsight

V některých případech se může stát, že budete chtít zjistit, jaké operace ovladač WASB spustil s Azure Storage. Pro stranu klienta vytváří ovladač WASB protokoly pro každou operaci systému souborů na úrovni **ladění** . WASB Driver používá log4j k řízení úrovně protokolování a výchozí hodnota je úroveň **informace** . Azure Storage protokoly analýzy na straně serveru najdete v tématu [protokolování Azure Storage Analytics](../../storage/common/storage-analytics-logging.md).

Vytvořený protokol bude vypadat nějak takto:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Zapnout protokol ladění WASB pro operace se soubory

1. Z webového prohlížeče přejděte na `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` , kde `CLUSTERNAME` je název vašeho clusteru Spark.

1. Přejděte na **Upřesnit spark2-log4j-Properties**.

    1. Upravit `log4j.appender.console.Threshold=INFO` na `log4j.appender.console.Threshold=DEBUG` .

    1. Přidat `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Přejděte na **Upřesnit livy2-log4j-Properties**.

    Přidat `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Uložte změny.

## <a name="additional-logging"></a>Dodatečné protokolování

Výše uvedené protokoly by měly poskytovat vysokou úroveň porozumění operacím systému souborů. Pokud výše uvedené protokoly stále neposkytují užitečné informace, nebo pokud chcete prozkoumat volání rozhraní API služby Blob Storage, přidejte `fs.azure.storage.client.logging=true` do `core-site` . Toto nastavení povolí protokoly Java SDK pro ovladač úložiště wasb a všechna volání na server BLOB Storage se vytisknou. Po dokončení šetření toto nastavení odeberte, protože by mohlo dojít k rychlému naplnění disku a zpomalení procesu.

Pokud je back-end Azure Data Lake na základě, použijte následující nastavení log4j pro komponentu (například Spark/tez/HDFS):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Vyhledejte protokoly v části `/var/log/adl/adl.log` .

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
