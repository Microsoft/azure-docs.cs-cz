---
title: Protokoly Apache Hive zaplňujících místo na disku – Azure HDInsight
description: Protokoly Apache Hive zaplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78943969"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénář: protokoly Apache Hive zaplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení pro problémy související s nedostatkem místa na disku v hlavních uzlech v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive/LLAP zabírají nechtěné protokoly v hlavních uzlech celé místo na disku. V důsledku toho mohou být zjištěny následující problémy.

1. Přístup SSH se nezdařil z důvodu nedostatku místa na hlavním uzlu.
2. Ambari poskytuje *chybu protokolu http: služba 503 není k dispozici*.

V `ambari-agent` protokolech se při potížích zobrazí následující.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Příčina

V pokročilých konfiguracích log4j podregistru se parametr *log4j. appender. rfa. MaxBackupIndex* vynechá. Způsobuje nekonečnou generaci souborů protokolu.

## <a name="resolution"></a>Řešení

1. Přejděte na souhrn komponenty podregistru na portálu Ambari a klikněte na `Configs` kartu.

2. Přejít na `Advanced hive-log4j` část v části Upřesnit nastavení.

3. Nastavte `log4j.appender.RFA` parametr jako RollingFileAppender. 

4. Nastavte `log4j.appender.RFA.MaxFileSize` a `log4j.appender.RFA.MaxBackupIndex` následujícím způsobem.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Nastavte `hive.root.logger` následujícím `INFO,RFA` způsobem. Výchozím nastavením je ladění, které způsobí, že se protokoly budou velmi velké.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Uložte konfigurace a restartujte požadované součásti.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
