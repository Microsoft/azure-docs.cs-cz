---
title: Balíček zabezpečení podniku pro Azure HDInsight
description: Přečtěte si informace o komponentách a verzích Balíček zabezpečení podniku ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 442c21c92ef2124ebef1889f99a8d2b806c8ce10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943303"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Balíček zabezpečení podniku pro Azure HDInsight

Enterprise Security je volitelný balíček, který můžete přidat do clusteru HDInsight jako součást pracovního postupu vytvoření clusteru. Balíček zabezpečení podniku podporuje:

* Integrace se službou Active Directory pro ověřování.

    V minulosti jste vytvořili clustery HDInsight s místními uživateli a místními uživateli SSH. Místní uživatel správce má přístup ke všem souborům, složkám, tabulkám a sloupcům.  Pomocí Balíček zabezpečení podniku můžete povolit řízení přístupu na základě role Azure integrací služby HDInsight s vašimi Azure Active Directory Domain Services.

    Další informace naleznete v tématu:

    * [Úvod do zabezpečení Apache Hadoop s využitím clusterů HDInsight připojených k doméně](./domain-joined/hdinsight-security-overview.md)

    * [Plánování clusterů Apache Hadoop připojených k doméně Azure v HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Konfigurace prostředí sandboxu připojeného k doméně](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

    * [Konfigurace clusterů HDInsight připojených k doméně pomocí Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorizace pro data

  * Integrace s Apache Ranger pro autorizaci pro podregistry, Spark SQL a přízové fronty.
  * Můžete nastavit řízení přístupu k souborům a složkám.

    Další informace najdete v tématu [Konfigurace zásad Apache Hive v HDInsight připojené k doméně](./domain-joined/apache-domain-joined-run-hive.md) .

* Prohlédněte si protokoly auditu a sledujte přístup a nakonfigurované zásady.

## <a name="supported-cluster-types"></a>Podporované typy clusterů

V současné době jsou Balíček zabezpečení podniku podporovány pouze následující typy clusterů:

* Hadoop (pouze HDInsight 3,6)
* Spark
* Kafka
* HBase
* Interaktivní dotaz

## <a name="support-for-azure-data-lake-storage"></a>Podpora Azure Data Lake Storage

Balíček zabezpečení podniku podporuje použití Azure Data Lake Storage jako primárního úložiště i úložiště doplňku.

## <a name="pricing-and-service-level-agreement-sla"></a>Ceny a smlouvy o úrovni služeb (SLA)

Informace o cenách a smlouvě SLA pro Balíček zabezpečení podniku najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
* [Poznámky k verzi Hortonworks přidružené k verzím Azure HDInsight](./hortonworks-release-notes.md)
* [Komponenty Apache ve službě HDInsight](./hdinsight-component-versioning.md)