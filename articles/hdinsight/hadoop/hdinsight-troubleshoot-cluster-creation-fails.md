---
title: Řešení chyb při vytváření clusteru s Azure HDInsight
description: Naučte se řešit problémy s vytvářením clusteru pro Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857245"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Řešení chyb při vytváření clusteru s Azure HDInsight

Následující problémy jsou nejběžnější hlavní příčiny selhání při vytváření clusteru:

- Problémy s oprávněním
- Omezení zásad prostředků
- Brány firewall
- Zámky prostředků
- Nepodporované verze součástí
- Omezení názvu účtu úložiště
- Výpadky služeb

## <a name="permissions-issues"></a>Problémy s oprávněními

Pokud používáte Data Lake Storage Gen 2, ujistěte se, že uživatelsky přiřazená spravovaná identita přiřazená ke clusteru HDInsight je v roli **Přispěvatel dat objektů BLOB úložiště** nebo v **roli vlastníka dat objektu BLOB úložiště**. Úplné pokyny k instalaci najdete v tématu [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) .

Pokud používáte Data Lake Storage Gen 1, přečtěte si [tady](../hdinsight-hadoop-use-data-lake-store.md)pokyny k instalaci a konfiguraci. Data Lake Storage Gen 1 se u clusterů HBA nepodporuje a v HDInsight verze 4,0 se nepodporuje.

Pokud používáte Azure Storage, ujistěte se, že je název účtu úložiště během vytváření clusteru platný.

## <a name="resource-policy-restrictions"></a>Omezení zásad prostředků

Zásady Azure založené na předplatném můžou odepřít vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.  

Obecně platí, že následující zásady mohou ovlivnit vytváření clusteru:

* Zásady zabraňující vytváření IP adres & nástrojů pro vyrovnávání zatížení v rámci předplatného.
* Zásada brání vytvoření účtu úložiště.
* Zásady zabraňující odstraňování síťových prostředků (IP adres/Load vyrovnávání zatížení).

## <a name="firewalls"></a>Brány firewall

Brány firewall ve vaší virtuální síti nebo účtu úložiště můžou odepřít komunikaci s IP adresami správy HDInsight.

Povolte provoz z IP adres v následující tabulce.

| Zdrojová IP adresa | Cíl | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Příchozí |
| 23.99.5.239 | *: 443 | Příchozí |
| 168.61.48.131 | *: 443 | Příchozí |
| 138.91.141.162 | *: 443 | Příchozí |

Přidejte taky IP adresy specifické pro oblast, ve které se cluster vytvoří. Seznam adres pro každou oblast Azure najdete v tématu věnovaném [IP adresám správy HDInsight](../hdinsight-management-ip-addresses.md) .

Pokud používáte Express Route nebo vlastní server DNS, přečtěte si téma [plánování virtuální sítě pro Azure HDInsight – připojení více sítí](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Zámky prostředků  

Zajistěte, aby [ve vaší virtuální síti a skupině prostředků](../../azure-resource-manager/resource-group-lock-resources.md)nejsou žádné zámky.  

## <a name="unsupported-component-versions"></a>Nepodporované verze součástí

Ujistěte se, že používáte [podporovanou verzi Azure HDInsight](../hdinsight-component-versioning.md) a všechny [součásti Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) ve vašem řešení.  

## <a name="storage-account-name-restrictions"></a>Omezení názvu účtu úložiště

Názvy účtů úložiště nesmí být delší než 24 znaků a nesmí obsahovat speciální znak. Tato omezení platí také pro výchozí název kontejneru v účtu úložiště.

## <a name="service-outages"></a>Výpadky služeb

Ověřte [stav Azure](https://status.azure.com/status) pro případné potenciální výpadky nebo problémy se službami.

## <a name="next-steps"></a>Další postup

* [Rozšíření Azure HDInsight pomocí virtuální síť Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
