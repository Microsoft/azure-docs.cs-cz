---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 339926fbd3c96f6f6c279d29676950b9915b4256
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87484155"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-07302020"></a>Datum vydání: 07/30/2020

Tato verze platí pro HDInsight 3,6 a 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="support-for-sparkcruise"></a>Podpora pro SparkCruise
SparkCruise je systém automatického využití výpočtů pro Spark. Vybere běžné dílčí výrazy, které se vyhodnotit na základě úlohy minulého dotazu. SparkCruise materializuje tyto dílčí výrazy jako součást zpracování dotazů a opakované použití výpočtu se automaticky aplikují na pozadí. Můžete využívat výhod SparkCruise bez jakýchkoli úprav kódu Sparku.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Podpora zobrazení podregistru pro HDInsight 4,0
Zobrazení podregistru Apache Ambari je navržené tak, aby vám usnadnilo vytváření, optimalizaci a spouštění dotazů na podregistr z webového prohlížeče. Zobrazení podregistru se nativně podporuje pro clustery HDInsight 4,0 od této verze. Neplatí pro existující clustery. K získání integrovaného zobrazení podregistru budete potřebovat vyřadit a znovu vytvořit cluster.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Podpora zobrazení tez pro HDInsight 4,0
Zobrazení Apache Tez slouží ke sledování a ladění provádění úlohy tez podregistru. Zobrazení tez se nativně podporuje pro HDInsight 4,0 od této verze. Neplatí pro existující clustery. Pokud chcete získat integrované zobrazení tez, musíte cluster vyřadit a znovu vytvořit.

## <a name="deprecation"></a>Vyřazení
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Ukončení podpory Sparku 2.1 a 2.2 v clusteru HDInsight 3.6 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark pomocí Sparku 2,1 a 2,2 ve službě HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2,3 ve službě HDInsight 3,6 pomocí června 30 2020, aby se předešlo potenciálnímu přerušení systému/podpory.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Ukončení podpory Sparku 2.3 v clusteru HDInsight 4.0 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark se Sparkem 2,3 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2.4 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Ukončení podpory Kafka 1.1 v clusteru HDInsight 4.0 Kafka
Od července 1 2020 nebudou zákazníci moci vytvářet nové clustery Kafka s Kafka 1,1 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Kafka 2.1 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.

## <a name="behavior-changes"></a>Změny chování
### <a name="ambari-stack-version-change"></a>Změna verze zásobníku Ambari
Z této verze se verze Ambari změnila ze 2. x. x. x na 4,1. K získání verze Ambari z uživatelského rozhraní Ambari se dá získat >.

## <a name="upcoming-changes"></a>Nadcházející změny
Žádné nadcházející nepotřebné změny, ke kterým byste měli věnovat pozornost.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

Pod JIRAs jsou pro podregistr back-Transported:
* [PODREGISTR-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [PODREGISTR-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [PODREGISTR-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [PODREGISTR-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [PODREGISTR-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [PODREGISTR-18786](https://issues.apache.org/jira/browse/HIVE-18786)

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.