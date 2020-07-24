---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006530"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Shrnutí

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-07132020"></a>Datum vydání: 07/13/2020

Tato verze platí pro HDInsight 3,6 a 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Podpora Customer Lockbox pro Microsoft Azure
Azure HDInsight teď podporuje Azure Customer Lockbox. Poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků. Používá se, když Microsoft inženýr potřebuje přístup k zákaznickým datům během žádosti o podporu. Další informace najdete v tématu [Customer Lockbox pro Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Zásady koncového bodu služby pro úložiště
Zákazníci teď můžou používat zásady koncového bodu služby (SEP) v podsíti clusteru HDInsight. Přečtěte si další informace o [zásadách koncového bodu služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Vyřazení
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Ukončení podpory Sparku 2.1 a 2.2 v clusteru HDInsight 3.6 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark pomocí Sparku 2,1 a 2,2 ve službě HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2,3 ve službě HDInsight 3,6 pomocí června 30 2020, aby se předešlo potenciálnímu přerušení systému/podpory.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Ukončení podpory Sparku 2.3 v clusteru HDInsight 4.0 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark se Sparkem 2,3 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2.4 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Ukončení podpory Kafka 1.1 v clusteru HDInsight 4.0 Kafka
Od července 1 2020 nebudou zákazníci moci vytvářet nové clustery Kafka s Kafka 1,1 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Kafka 2.1 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.

## <a name="behavior-changes"></a>Změny chování
Žádné změny chování, ke kterým byste měli věnovat pozornost.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Možnost výběru různých Zookeeper SKU pro služby Spark, Hadoop a ML
HDInsight dnes nepodporuje změnu Zookeeper SKU pro typy clusterů Spark, Hadoop a ML. Používá A2_v2 SKU/a2 pro uzly Zookeeper a zákazníkům se za ně neúčtují poplatky. V nadcházející verzi budou zákazníci moct podle potřeby měnit Zookeeper SKU pro služby Spark, Hadoop a ML. Zookeeper uzly s jinou skladovou jednotkou než A2_v2/a2 budou účtovány. Výchozí SKU bude i nadále A2_V2/a2 a zadarmo.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Problém konektoru pro pevný datový sklad
Došlo k potížím při použitelnosti konektoru skladu z podregistru v předchozí verzi. Problém byl vyřešen. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Pevný Poznámkový blok Zeppelin zkrátí problém s počátečními nulami
Zeppelin nesprávně zkrátila úvodní nuly ve výstupu tabulky pro formát řetězce. V této verzi jsme tento problém vyřešili.

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.
