---
title: Podpora softwaru s otevřeným zdrojovým kódem v Azure HDInsight
description: Microsoft Azure poskytuje obecnou úroveň podpory pro technologie s otevřeným zdrojovým kódem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772168"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Podpora softwaru s otevřeným zdrojovým kódem v Azure HDInsight

Služba Microsoft Azure HDInsight využívá prostředí open source technologií vytvořených kolem Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro technologie s otevřeným zdrojovým kódem. Další informace najdete v části **Obor podpory** v [nejčastějších dotazech k podpoře Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro vestavěné součásti.

## <a name="components"></a>Komponenty

Ve službě HDInsight jsou k dispozici dva typy komponent s otevřeným zdrojovým kódem:

### <a name="built-in-components"></a>Vestavěné komponenty

Tyto součásti jsou předinstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří následující součásti:

* [Apache Hadoop příze](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Správce zdrojů.
* Dotazovací jazyk [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Úplný seznam součástí clusteru je k dispozici v [části Jaké jsou komponenty apache hadoop a verze dostupné s HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Vlastní součásti

Jako uživatel clusteru můžete nainstalovat nebo použít ve své pracovní zátěži libovolnou komponentu, která je dostupná v komunitě nebo kterou jste vytvořili.

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporovány. Podpora společnosti Microsoft pomáhá izolovat a vyřešit problémy související s těmito součástmi.
>
> Vlastní součásti obdrží komerčně přiměřenou podporu, která vám pomůže dále řešit problém. Problém se může vyřešit podpora společnosti Microsoft. Nebo vás mohou požádat, abyste zapojili dostupné kanály pro technologie s otevřeným zdrojovým kódem, kde se nacházejí hluboké odborné znalosti pro tuto technologii. Lze použít mnoho komunitních webů. Příkladem je [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) a [Přetečení zásobníku](https://stackoverflow.com).
>
> Projekty Apache mají také projektové stránky na [webových stránkách Apache](https://apache.org). Příkladem je [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Použití komponent

Služba HDInsight poskytuje několik způsobů použití vlastních součástí. Platí stejná úroveň podpory bez ohledu na to, jak je komponenta používána nebo nainstalována v clusteru. Následující tabulka popisuje nejběžnější způsoby použití vlastních součástí v clusterech HDInsight:

|Využití |Popis |
|---|---|
|Odeslání úlohy|Hadoop nebo jiné typy úloh, které spouštějí nebo používají vlastní součásti, lze odeslat do clusteru.|
|Přizpůsobení clusteru|Během vytváření clusteru můžete zadat další nastavení a vlastní součásti, které jsou nainstalovány v uzlech clusteru.|
|Ukázky|U oblíbených vlastních součástí může společnost Microsoft a další uživatelé poskytnout ukázky toho, jak lze tyto součásti použít v clusterech HDInsight. Tyto vzorky jsou poskytovány bez podpory.|

## <a name="next-steps"></a>Další kroky

* [Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md)
* [Vývoj skriptovacích akčních skriptů pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Zabezpečená správa prostředí Pythonu v Azure HDInsightu s využitím akce skriptu](./spark/apache-spark-python-package-installation.md)
