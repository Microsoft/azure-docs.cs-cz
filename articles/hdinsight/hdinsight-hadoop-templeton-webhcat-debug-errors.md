---
title: Rady pro pochopení a řešení chyb WebHCat v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak k o běžné chyby vrácené WebHCat v HDInsight a způsob jejich řešení.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 03805086bf1cff3f6d2a27a46960d736538bf57b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Rady pro pochopení a řešení chyb oznámených z WebHCat v HDInsight

Další informace o chyb oznámených při použití WebHCat s HDInsight a způsob jejich řešení. WebHCat se používá interně pomocí klienta nástroje, jako je Azure PowerShell a nástrojů Data Lake pro Visual Studio.

## <a name="what-is-webhcat"></a>Co je WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) je rozhraní REST API pro [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabulka a vrstva správy úložiště pro Hadoop. WebHCat je povoleno ve výchozím nastavení v clusterech prostředí HDInsight a různé nástroje používá k odesílání úloh, bez přihlášení do clusteru můžete získat stav úlohy, atd.

## <a name="modifying-configuration"></a>Změny konfigurace

> [!IMPORTANT]
> Několik chyb uvedených v tomto dokumentu dojít, protože byla překročena maximální nakonfigurované. Při řešení krok uvádí, že můžete změnit hodnotu, musí používat jednu z těchto k provedení změn:

* Pro **Windows** clustery: použijte akci skriptu ke konfiguraci hodnota během vytváření clusteru. Další informace najdete v tématu [vývoj akcí skriptů](hdinsight-hadoop-script-actions.md).

* Pro **Linux** clustery: použití Ambari (web nebo REST API) změňte hodnotu. Další informace najdete v tématu [spravovat HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Výchozí konfigurace

Pokud se překročí následující výchozí hodnoty, může snížit výkon WebHCat nebo způsobit chyby:

| Nastavení | Co dělá | Výchozí hodnota |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Maximální počet úloh, které mohou být souběžně aktivní (čekající na vyřízení nebo spuštěné) |10 000 |
| [templeton.exec.max-procs][max-procs] |Maximální počet požadavků, které lze zpracovat současně |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Počet dní, které úlohy historii jsou uchovány |7 dní |

## <a name="too-many-requests"></a>Příliš mnoho požadavků

**Kód stavu HTTP**: 429

| Příčina | Řešení |
| --- | --- |
| Překročili jste maximální souběžných požadavků zpracovaných WebHCat za minutu (výchozí 20) |Snížení velikosti pracovní zátěže zajistit, že neodešlete více než maximální počet souběžných požadavků nebo zvyšte limit počtu souběžných požadavků úpravou `templeton.exec.max-procs`. Další informace najdete v tématu [Modifying konfigurace](#modifying-configuration) |

## <a name="server-unavailable"></a>Server není k dispozici

**Kód stavu HTTP**: 503

| Příčina | Řešení |
| --- | --- |
| Tento kód stavu obvykle dojde během převzetí služeb při selhání mezi primárním a sekundárním HeadNode pro cluster |Počkejte 2 minuty a potom operaci opakujte |

## <a name="bad-request-content-could-not-find-job"></a>Chybný požadavek obsahu: Nelze najít úlohu

**Kód stavu HTTP**: 400

| Příčina | Řešení |
| --- | --- |
| Podrobnosti úlohy byla vyčištěna podle historie úlohy čisticí |Výchozí doba uchování historie úlohy je 7 dní. Výchozí dobu uchování lze změnit úpravou `mapreduce.jobhistory.max-age-ms`. Další informace najdete v tématu [Modifying konfigurace](#modifying-configuration) |
| Úlohy byl ukončen z důvodu selhání |Opakujte odeslání úlohy pro až dvě minuty. |
| Neplatné id práce. byl použit. |Kontrola, zda je správný id úlohy |

## <a name="bad-gateway"></a>Chybná brána

**Kód stavu HTTP**: 502

| Příčina | Řešení |
| --- | --- |
| Uvolňování paměti interní dochází v rámci procesu WebHCat |Počkejte uvolňování paměti pro dokončení nebo restartujte službu WebHCat |
| Časový limit čekání na odpověď ze služby ResourceManager. K této chybě může dojít, když počet aktivních aplikací přejde nakonfigurované maximum (výchozí 10 000) |Počkejte aktuálně spuštěné úlohy k dokončení, nebo zvyšte limit souběžných úloh úpravou `yarn.scheduler.capacity.maximum-applications`. Další informace najdete v tématu [Modifying konfigurace](#modifying-configuration) části. |
| Pokus o načtení všech úloh prostřednictvím [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) volání při `Fields` je nastaven na `*` |Nenačítat *všechny* podrobnosti úlohy. Místo toho použijte `jobid` načíst podrobnosti úlohy pouze větší než id určité úlohy. Nebo, nepoužívejte `Fields` |
| Služba WebHCat je mimo provoz během převzetí služeb při selhání HeadNode |Počkejte dvou minut a opakujte operaci |
| Existuje více než 500 čekající úlohy, odeslané prostřednictvím WebHCat |Počkejte na dokončení aktuálně čeká na provedení úloh před odesláním další úlohy |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
