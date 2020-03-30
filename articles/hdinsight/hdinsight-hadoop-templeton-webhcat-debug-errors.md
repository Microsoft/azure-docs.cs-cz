---
title: Pochopení a řešení chyb WebHCat na HDInsight – Azure
description: Přečtěte si, jak o běžných chybách vrácených WebHCat na HDInsight a jak je vyřešit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638846"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Seznámení s chybami přijatými z WebHCatu v HDInsightu a jejich řešení

Přečtěte si o chybách, které se při používání WebHCatu s HDInsightem zobrazily, a jak je vyřešit. WebHCat se používá interně pomocí nástrojů na straně klienta, jako je Azure PowerShell a Nástroje datového jezera pro Visual Studio.

## <a name="what-is-webhcat"></a>Co je WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) je REST API pro [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabulka a vrstva správy úložiště pro Apache Hadoop. WebHCat je ve výchozím nastavení povolen v clusterech HDInsight a používá se různými nástroji k odesílání úloh, získání stavu úlohy a tak dále, aniž by se přihlásil do clusteru.

## <a name="modifying-configuration"></a>Změna konfigurace

Několik chyb uvedených v tomto dokumentu dochází, protože bylo překročeno nakonfigurované maximum. Když krok řešení uvádí, že můžete změnit hodnotu, použijte Apache Ambari (web nebo REST API) upravit hodnotu. Další informace najdete [v tématu Správa HDInsightu pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Výchozí konfigurace

Pokud jsou překročeny následující výchozí hodnoty, může dojít ke snížení výkonu webhcat nebo způsobit chyby:

| Nastavení | Co dělá | Výchozí hodnota |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-aplikace][maximum-applications] |Maximální počet úloh, které mohou být aktivní současně (čekající nebo spuštěné) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maximální počet požadavků, které mohou být doručeny současně |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Počet dní, po které se historie úloh uchovává |7 dní |

## <a name="too-many-requests"></a>Příliš mnoho požadavků

**Stavový kód HTTP**: 429

| Příčina | Řešení |
| --- | --- |
| Překročili jste maximální souběžné požadavky obsluhované webhcatem za minutu (výchozí 20) |Snižte pracovní vytížení a ujistěte se, že neodešlete více než maximální `templeton.exec.max-procs`počet souběžných požadavků nebo nezvýšíte limit souběžných požadavků úpravou . Další informace naleznete [v tématu Úprava konfigurace](#modifying-configuration) |

## <a name="server-unavailable"></a>Server není k dispozici.

**Stavový kód HTTP**: 503

| Příčina | Řešení |
| --- | --- |
| K tomuto stavovému kódu obvykle dochází během převzetí služeb při selhání mezi primární a sekundární název HeadNode pro cluster. |Počkejte dvě minuty a opakujte operaci. |

## <a name="bad-request-content-could-not-find-job"></a>Chybný obsah požadavku: Nelze najít práci

**Stavový kód HTTP:** 400

| Příčina | Řešení |
| --- | --- |
| Podrobnosti o úloze byly vyčištěny čističem historie práce |Výchozí doba uchovávání pro historii úloh je 7 dní. Výchozí dobu uchování lze změnit `mapreduce.jobhistory.max-age-ms`úpravou . Další informace naleznete [v tématu Úprava konfigurace](#modifying-configuration) |
| Práce byla zabita kvůli převzetí služeb při selhání |Opakování odeslání úlohy po dobu až dvou minut |
| Bylo použito Neplatné ID úlohy. |Zkontrolujte, zda je ID úlohy správné |

## <a name="bad-gateway"></a>Špatná brána

**Stavový kód HTTP**: 502

| Příčina | Řešení |
| --- | --- |
| V rámci procesu WebHCat dochází k internímu uvolňování paměti. |Počkejte na dokončení uvolňování paměti nebo restartování služby WebHCat |
| Časový režim čekání na odpověď ze služby ResourceManager. K této chybě může dojít, když počet aktivních aplikací přejde na konfigurované maximum (výchozí hodnota 10 000). |Počkejte na aktuálně spuštěné úlohy k dokončení nebo `yarn.scheduler.capacity.maximum-applications`zvýšení limitu souběžných úloh úpravou . Další informace naleznete v části [Úpravy konfigurace.](#modifying-configuration) |
| Pokus o načtení všech úloh prostřednictvím `Fields` volání [GET /jobs,](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) zatímco je nastavenna na`*` |Nenačítejte *všechny* podrobnosti o práci. Místo `jobid` toho použít k načtení podrobností pro úlohy pouze větší než určité ID úlohy. Nebo nepoužívejte`Fields` |
| Služba WebHCat je během převzetí služeb při selhání HeadNode vypnutá. |Počkejte dvě minuty a opakujte operaci |
| Existuje více než 500 nevyřízených úloh odeslaných prostřednictvím WebHCat |Počkejte, až budou dokončeny aktuálně nevyřízené úlohy, než odešlete více úloh |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
