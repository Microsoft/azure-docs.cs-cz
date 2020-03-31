---
title: Protokoly Apache Hive zaplňují místo na disku – Azure HDInsight
description: Protokoly Apache Hive zaplňují místo na disku v hlavní uzly v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943969"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénář: Protokoly Apache Hive zaplňují místo na disku v uzlech Head v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů souvisejících s nedostatečným místem na disku v hlavových uzlech v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive/LLAP zabírají nežádoucí protokoly celé místo na disku v hlavních uzlech. Vzhledem k tomu, následující problémy by mohly být viděny.

1. Přístup SSH se nezdaří, protože v hlavním uzlu nezbývá místo.
2. Ambari dává *HTTP ERROR: 503 Služba není k dispozici*.

Protokoly `ambari-agent` by zobrazit následující, když dojde k problému.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Příčina

V pokročilých konfiguracích Hive-log4j je parametr *log4j.appender.RFA.MaxBackupIndex* vynechán. To způsobuje nekonečné generování souborů protokolu.

## <a name="resolution"></a>Řešení

1. Přejděte na souhrn komponent Hive na portálu `Configs` Ambari a klikněte na kartu.

2. Přejděte `Advanced hive-log4j` do sekce v rozšířeném nastavení.

3. Nastavte `log4j.appender.RFA` parametr jako RollingFileAppender. 

4. Nastavte `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` a takto.

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
5. Nastavte `hive.root.logger` `INFO,RFA` takto. Výchozí nastavení je LADĚNÍ, což způsobí, že protokoly stanou velmi velké.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Uložte konfigurace a restartujte požadované součásti.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
