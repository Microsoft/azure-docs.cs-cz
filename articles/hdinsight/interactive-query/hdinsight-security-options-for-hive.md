---
title: Možnosti zabezpečení pro podregistr ve službě Azure HDInsight
description: Možnosti zabezpečení pro podregistr v clusterech Standard a ESP.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869714"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Možnosti zabezpečení pro podregistr ve službě Azure HDInsight

Tento dokument popisuje doporučené možnosti zabezpečení pro podregistr v HDInsight. Tyto možnosti se dají nakonfigurovat přes Ambari.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="' Možnosti zabezpečení pro podregistr '" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2 ověřování

U standardních clusterů je doporučené nastavení pro ověřování HiveServer2 výchozím nastavením, které není žádné. Pokud chcete povolit ověřování, doporučujeme upgradovat na cluster [ESP](../domain-joined/hdinsight-security-overview.md) (balíček zabezpečení podniku). 

Pro clustery ESP je ve výchozím nastavení povolené ověřování [protokolu Kerberos](https://web.mit.edu/Kerberos/) . Moduly PAM a vlastní schémata ověřování nejsou podporované.

## <a name="hiveserver2-authorization"></a>Autorizace HiveServer2

Ve standardních clusterech je výchozí nastavení žádné. [SqlStdAuth (ověřování na základě standardů SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) se dá povolit. Autorizace prostřednictvím [Apache Ranger](https://ranger.apache.org/) se pro standardní clustery nepodporuje. Pro autorizaci Ranger doporučujeme upgradovat na cluster ESP. 

V případě clusterů ESP je autorizace prostřednictvím Ranger ve výchozím nastavení povolená. 


## <a name="ssl-encryption-for-hiveserver2"></a>Šifrování SSL pro HiveServer2

Povolení protokolu SSL Hiveserver2 se nedoporučuje pro clustery Standard a ESP. Protokol SSL je místo toho povolený v bráně. [Šifrování v přenosech](../domain-joined/encryption-in-transit.md) se dá povolit, aby se zašifroval komunikace mezi uzly clusteru pomocí [protokolu Internet Protocol Security (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Další kroky
* [Přehled ověřování HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Přehled autorizace HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Povolení ověřování v registru založeného na standardech SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger s podregistrem](../domain-joined/apache-domain-joined-run-hive.md)
