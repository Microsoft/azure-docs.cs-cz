---
title: Podpora open source softwaru ve službě Azure HDInsight
description: Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fe9ccc50d6196997863fbf228fbb76913a0d0330
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013866"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Podpora open source softwaru ve službě Azure HDInsight

Služba Microsoft Azure HDInsight používá prostředí Open Source technologií vytvořených v Apache Hadoop. Microsoft Azure poskytuje obecnou úroveň podpory pro open source technologie. Další informace najdete v části věnované **oboru podpory** v tématu [Nejčastější dotazy týkající se podpory Azure](https://azure.microsoft.com/support/faq/). Služba HDInsight poskytuje další úroveň podpory pro integrované součásti.

## <a name="components"></a>Komponenty

Ve službě HDInsight jsou k dispozici dva typy open source komponent:

### <a name="built-in-components"></a>Integrované komponenty

Tyto součásti jsou předem nainstalovány v clusterech HDInsight a poskytují základní funkce clusteru. Do této kategorie patří tyto komponenty:

* [Apache HADOOP nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Správce prostředků.
* Dotazovací jazyk podregistru [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/)

Úplný seznam součástí clusteru je k dispozici v části [Jaké jsou Apache Hadoop komponenty a verze, které jsou k dispozici v HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Vlastní součásti

Jako uživatel clusteru můžete v rámci své úlohy nainstalovat nebo použít všechny součásti dostupné ve komunitě nebo vytvořené vámi.

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporované. Podpora Microsoftu pomáhá izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. Je možné, že podpora Microsoftu problém vyřešit. Případně vás můžou požádat, abyste provedli dostupné kanály pro technologie Open Source, kde se nachází hlubokou odbornost této technologie. Je možné použít mnoho webů komunity. Příklady jsou [Microsoft Q&Stránka s otázkou pro HDInsight](/answers/topics/azure-hdinsight.html) a [Stack Overflow](https://stackoverflow.com).
>
> Projekty Apache také obsahují projektové weby na [webu Apache](https://apache.org). Příkladem je [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Použití komponenty

Služba HDInsight nabízí několik způsobů použití vlastních komponent. Stejná úroveň podpory platí bez ohledu na to, jak se součást používá nebo je v clusteru nainstalovaná. Následující tabulka popisuje nejběžnější způsoby použití vlastních komponent v clusterech HDInsight:

|Využití |Description |
|---|---|
|Odeslání úlohy|Cluster Hadoop nebo jiné typy úloh, které spouštějí nebo používají vlastní komponenty, lze odeslat do clusteru.|
|Přizpůsobení clusteru|Během vytváření clusteru můžete zadat další nastavení a vlastní součásti, které jsou nainstalované na uzlech clusteru.|
|ukázky|V případě oblíbených vlastních komponent mohou společnosti Microsoft a jiné poskytnout ukázky, jak lze tyto komponenty použít v clusterech HDInsight. Tyto ukázky jsou k dispozici bez podpory.|

## <a name="next-steps"></a>Další kroky

* [Přizpůsobení clusterů Azure HDInsight pomocí akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md)
* [Vývoj skriptů akcí skriptu pro HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Zabezpečená správa prostředí Pythonu v Azure HDInsightu s využitím akce skriptu](./spark/apache-spark-python-package-installation.md)