---
title: Vysvětlení a řešení chyb WebHCat na HDInsight – Azure
description: Zjistěte, jak o běžné chyby vrácené WebHCat na HDInsight a jejich řešení.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 2c4730b3ec84ca14bcc3e93ed82faf37b15970d7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010367"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Pochopení a vyřešení chyb přijatých z WebHCat v HDInsight

Další informace o chyb oznámených při použití WebHCat s HDInsight a způsob jejich řešení. WebHCat se používá interně pomocí nástrojů na straně klienta, jako je Azure PowerShell a nástrojů Data Lake pro Visual Studio.

## <a name="what-is-webhcat"></a>Co je WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) je rozhraní REST API pro [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabulky a vrstva správy úložiště pro Hadoop. WebHCat je povolené ve výchozím nastavení v clusterech HDInsight a různé nástroje používají k odesílání úloh, není nutné se připojit ke clusteru můžete získat stav úlohy, atd.

## <a name="modifying-configuration"></a>Změna konfigurace

> [!IMPORTANT]
> Několik chyb uvedených v tomto dokumentu dojít, protože se překročila nakonfigurované maximum. Když krok řešení uvádí, že můžete změnit hodnotu, musíte použít jednu z následujících provádět změny:

* Pro **Windows** clustery: nakonfigurujte hodnotu při vytváření clusteru pomocí skriptových akcí. Další informace najdete v tématu [vývoj akcí skriptů](hdinsight-hadoop-script-actions.md).

* Pro **Linux** clustery: použití Ambari (web nebo rozhraní REST API) ke změně hodnoty. Další informace najdete v tématu [Správa HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Výchozí konfigurace

Překročení následujících výchozích hodnot může snížit výkon WebHCat nebo dojít k chybám:

| Nastavení | Co dělá | Výchozí hodnota |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Maximální počet úloh, které mohou být souběžně aktivní (čekající na vyřízení nebo spuštěné) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maximální počet požadavků, které se dají obsluhovat současně |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Počet dní, které historie úlohy se zachovají. |7 dní |

## <a name="too-many-requests"></a>Příliš mnoho požadavků

**Kód stavu HTTP**: 429

| Příčina | Řešení |
| --- | --- |
| Překročili jste maximální souběžných žádostí za minutu (20 výchozí nastavení) obsluhovat WebHCat |Snižte úloh, abyste zajistili, že neodešlete více než maximální počet souběžných požadavků nebo zvyšte limit souběžných žádostí úpravou `templeton.exec.max-procs`. Další informace najdete v tématu [změna konfigurace](#modifying-configuration) |

## <a name="server-unavailable"></a>Server není k dispozici

**Kód stavu HTTP**: 503

| Příčina | Řešení |
| --- | --- |
| Tento kód stavu obvykle dochází při převzetí služeb při selhání mezi primární a sekundární hlavní uzel clusteru |Počkejte 2 minuty a potom operaci opakujte |

## <a name="bad-request-content-could-not-find-job"></a>Chybný požadavek obsahu: Nelze najít úlohu

**Kód stavu HTTP**: 400

| Příčina | Řešení |
| --- | --- |
| Podrobnosti úlohy nebyly vyčištěny pomocí historie úloh čisticí |Výchozí době uchování historie úlohy je 7 dní. Úpravou můžete změnit výchozí dobu uchování `mapreduce.jobhistory.max-age-ms`. Další informace najdete v tématu [změna konfigurace](#modifying-configuration) |
| Úlohy byl ukončen z důvodu převzetí služeb při selhání |Opakujte odeslání úlohy pro až dvě minuty |
| Id úlohy je neplatné. byl použit. |Zkontrolujte, jestli je správný id úlohy |

## <a name="bad-gateway"></a>Chybná brána

**Kód stavu HTTP**: 502

| Příčina | Řešení |
| --- | --- |
| Interní uvolňování paměti dochází v rámci procesu WebHCat |Počkejte, uvolňování paměti na dokončení nebo restartujte službu WebHCat |
| Časový limit čekání na odpověď ze služby Správce prostředků. Této chybě může dojít, když se počet aktivních aplikací dostane nakonfigurované maximum (výchozí hodnota 10 000) |Počkejte právě probíhajících úloh dokončíte. nebo zvýšení limitu souběžných úloh tak, že upravíte `yarn.scheduler.capacity.maximum-applications`. Další informace najdete v tématu [změna konfigurace](#modifying-configuration) oddílu. |
| Při pokusu o načtení všech úloh prostřednictvím [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) volání při `Fields` je nastavena na `*` |Nepřijmou *všechny* podrobnosti úlohy. Místo toho použijte `jobid` k načtení podrobností pro úlohy pouze větší než id určité úlohy. Nebo, nepoužívejte `Fields` |
| Služba WebHCat je mimo provoz během převzetí služeb při selhání hlavního uzlu |Počkejte po dobu dvou minut a zkuste operaci zopakovat |
| Existuje více než 500 čekající úlohy odeslat prostřednictvím WebHCat |Počkejte na dokončení aktuálně čeká na provedení úloh před odesláním další úlohy |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
