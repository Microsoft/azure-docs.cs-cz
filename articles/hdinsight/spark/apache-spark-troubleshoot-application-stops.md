---
title: Po 24 dnech v Azure HDInsight se aplikace Apache Spark streaming zastaví.
description: Po 24 dnech se zastaví aplikace streamování Apache Spark a v souborech protokolu nejsou žádné chyby.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929472"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scénář: aplikace Apache Spark streaming se zastaví po spuštění po dobu 24 dnů ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Po 24 dnech se zastaví aplikace streamování Apache Spark a v souborech protokolu nejsou žádné chyby.

## <a name="cause"></a>Příčina

`livy.server.session.timeout`Hodnota určuje, jak dlouho má Apache Livy počkat na dokončení relace. Jakmile délka relace dosáhne `session.timeout` hodnoty, dojde k automatickému usmrcení relace Livy a aplikace.

## <a name="resolution"></a>Řešení

V případě dlouhotrvajících úloh zvyšte hodnotu `livy.server.session.timeout` pomocí uživatelského rozhraní Ambari. K konfiguraci Livy můžete přistupovat z uživatelského rozhraní Ambari pomocí adresy URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Nahraďte `<yourclustername>` názvem vašeho clusteru HDInsight, jak je znázorněno na portálu.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]