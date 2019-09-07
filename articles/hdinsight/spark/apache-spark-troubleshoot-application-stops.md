---
title: Po 24 dnech v Azure HDInsight se aplikace Apache Spark streaming zastaví.
description: Po 24 dnech se zastaví aplikace streamování Apache Spark a v souborech protokolu nejsou žádné chyby.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: c513c5df0d83eb0049683f88d85e8a1c41fd0bf0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736298"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scénář: Po 24 dnech ve službě Azure HDInsight se aplikace pro streamování Apache Spark přestane spouštět.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Po 24 dnech se zastaví aplikace streamování Apache Spark a v souborech protokolu nejsou žádné chyby.

## <a name="cause"></a>Příčina

Hodnota `livy.server.session.timeout` určuje, jak dlouho má Apache Livy počkat na dokončení relace. Jakmile délka relace dosáhne `session.timeout` hodnoty, dojde k automatickému usmrcení relace Livy a aplikace.

## <a name="resolution"></a>Řešení

V případě dlouhotrvajících úloh zvyšte hodnotu `livy.server.session.timeout` pomocí uživatelského rozhraní Ambari. K konfiguraci Livy můžete přistupovat z uživatelského rozhraní Ambari pomocí adresy URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`.

Nahraďte `<yourclustername>` názvem vašeho clusteru HDInsight, jak je znázorněno na portálu.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
