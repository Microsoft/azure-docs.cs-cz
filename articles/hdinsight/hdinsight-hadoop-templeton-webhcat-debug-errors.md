---
title: Pochopení a řešení chyb WebHCat ve službě HDInsight – Azure
description: Naučte se o běžných chybách vrácených WebHCat ve službě HDInsight a o tom, jak je vyřešit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: dbae7254949eaa642b8344b4e550de4a44b2f33f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545969"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Seznámení s chybami přijatými z WebHCatu v HDInsightu a jejich řešení

Přečtěte si o chybách přijatých při používání WebHCat se službou HDInsight a o tom, jak je vyřešit. WebHCat se používá interně pomocí nástrojů na straně klienta, jako je Azure PowerShell a Data Lake nástrojů pro Visual Studio.

## <a name="what-is-webhcat"></a>Co je WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) je REST API pro [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabulku a vrstvu správy úložiště pro Apache Hadoop. WebHCat je ve výchozím nastavení povolená v clusterech HDInsight a používá se v různých nástrojích k odesílání úloh, získávání stavu úlohy a tak dále, bez přihlášení ke clusteru.

## <a name="modifying-configuration"></a>Úprava konfigurace

Několik chyb uvedených v tomto dokumentu je způsobeno tím, že bylo překročeno nakonfigurované maximum. Pokud vás krok řešení zmiňuje o tom, že hodnotu můžete změnit, použijte Apache Ambari (web nebo REST API) a změňte hodnotu. Další informace najdete v tématu [Správa HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

### <a name="default-configuration"></a>Výchozí konfigurace

Pokud dojde k překročení následujících výchozích hodnot, může dojít ke snížení výkonu WebHCat nebo k chybám:

| Nastavení | Co dělá | Výchozí hodnota |
| --- | --- | --- |
| [příze. Scheduler. Capacity. Max – aplikace][maximum-applications] |Maximální počet úloh, které mohou být aktivní souběžně (čeká na vyřízení nebo spuštění) |10 000 |
| [templeton.exec. Max-procs][max-procs] |Maximální počet požadavků, které mohou být souběžně obsluhovány |20 |
| [MapReduce. jobhistory. Max – stáří-MS][max-age-ms] |Počet dní, po který se zachová historie úloh |7 dní |

## <a name="too-many-requests"></a>Příliš mnoho žádostí

**Stavový kód HTTP** : 429

| Příčina | Řešení |
| --- | --- |
| Překročili jste maximální počet souběžných požadavků zpracovaných WebHCat za minutu (výchozí 20). |Snižte své zatížení, abyste se ujistili, že neodesíláte více než maximální počet souběžných požadavků, nebo pokud chcete zvýšit limit souběžných požadavků úpravou `templeton.exec.max-procs` . Další informace najdete v tématu [Úprava konfigurace](#modifying-configuration) . |

## <a name="server-unavailable"></a>Server není k dispozici

**Stavový kód HTTP** : 503

| Příčina | Řešení |
| --- | --- |
| Tento stavový kód se obvykle objevuje během převzetí služeb při selhání mezi primárním a sekundárním hlavnímu uzlu clusteru. |Počkejte dvě minuty a potom zkuste operaci zopakovat. |

## <a name="bad-request-content-could-not-find-job"></a>Chybný obsah žádosti: nepovedlo se najít úlohu.

**Stavový kód HTTP** : 400

| Příčina | Řešení |
| --- | --- |
| Podrobnosti o úloze byly vyčištěny čisticí historií úlohy. |Výchozí doba uchování pro historii úlohy je 7 dní. Výchozí dobu uchování lze změnit úpravou `mapreduce.jobhistory.max-age-ms` . Další informace najdete v tématu [Úprava konfigurace](#modifying-configuration) . |
| Úloha byla ukončena z důvodu převzetí služeb při selhání. |Opakovat odeslání úlohy po dobu až dvou minut |
| Bylo použito neplatné ID úlohy. |Zkontroluje, jestli je ID úlohy správné. |

## <a name="bad-gateway"></a>Chybná brána

**Stavový kód HTTP** : 502

| Příčina | Řešení |
| --- | --- |
| V rámci procesu WebHCat dochází k internímu uvolňování paměti. |Počkejte, až se uvolňování paměti dokončí, nebo restartujte službu WebHCat. |
| Vypršel časový limit čekání na odpověď ze služby ResourceManager. K této chybě může dojít v případě, že počet aktivních aplikací překročí nakonfigurované maximum (výchozí 10 000). |Počkejte, než se aktuálně spuštěné úlohy dokončí, nebo zvyšte limit souběžnosti úloh úpravou `yarn.scheduler.capacity.maximum-applications` . Další informace najdete v části [Úprava konfiguračního](#modifying-configuration) oddílu. |
| Probíhá pokus o načtení všech úloh prostřednictvím volání metody [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) , zatímco `Fields` je nastaveno na `*` |Nenačte podrobnosti o *všech* úlohách. Místo toho použijte `jobid` k načtení podrobností pro úlohy, které jsou pouze větší než určité ID úlohy. Nebo nepoužívejte `Fields` |
| Služba WebHCat je během převzetí služeb při selhání hlavnímu uzlu vypnutá. |Počkejte dvě minuty a zkuste operaci zopakovat. |
| Prostřednictvím WebHCat bylo odesláno více než 500 nedokončených úloh. |Před odesláním dalších úloh počkejte na dokončení probíhajících úloh. |

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml