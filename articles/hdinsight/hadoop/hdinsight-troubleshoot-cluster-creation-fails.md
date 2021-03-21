---
title: Řešení chyb při vytváření clusteru s Azure HDInsight
description: Naučte se řešit problémy s vytvářením clusteru Apache pro Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: f909f5714a049f63032c8a23ca1731777a40d332
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702857"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Řešení chyb při vytváření clusteru s Azure HDInsight

Následující problémy jsou nejběžnější hlavní příčiny selhání při vytváření clusteru:

- Problémy s oprávněním
- Omezení zásad prostředků
- brány firewall,
- Zámky prostředků
- Nepodporované verze součástí
- Omezení názvu účtu úložiště
- Výpadky služeb

## <a name="permissions-issues"></a>Problémy s oprávněními

Pokud používáte Azure Data Lake Storage Gen2 a obdržíte chybu: " `AmbariClusterCreationFailedErrorCode` :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", otevřete Azure Portal, přejděte na svůj účet úložiště a v části Access Control (IAM) Zkontrolujte, že **Přispěvatel dat objektu BLOB úložiště** nebo role **vlastníka dat objektu BLOB úložiště** přiřadili přístup k **spravované identitě** , která je přiřazená k tomuto předplatnému uživateli. Podrobné pokyny najdete v tématu [Nastavení oprávnění pro spravovanou identitu na data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) .

Pokud používáte Azure Data Lake Storage Gen1, přečtěte si téma pokyny k instalaci a konfiguraci [použití Azure Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md). Data Lake Storage Gen1 se u clusterů HBA nepodporuje a v HDInsight verze 4,0 se nepodporuje.

Pokud používáte Azure Storage, ujistěte se, že je název účtu úložiště během vytváření clusteru platný.

## <a name="resource-policy-restrictions"></a>Omezení zásad prostředků

Zásady Azure založené na předplatném můžou odepřít vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.  

Obecně platí, že následující zásady mohou ovlivnit vytváření clusteru:

* Zásady zabraňující vytváření IP adres & nástrojů pro vyrovnávání zatížení v rámci předplatného.
* Zásada brání vytvoření účtu úložiště.
* Zásady zabraňující odstraňování síťových prostředků (IP adres/Load vyrovnávání zatížení).

## <a name="firewalls"></a>brány firewall,

Brány firewall ve vaší virtuální síti nebo účtu úložiště můžou odepřít komunikaci s IP adresami správy HDInsight.

Povolte provoz z IP adres v následující tabulce.

| Zdrojová IP adresa | Cíl | Směr |
|---|---|---|
| 168.61.49.99 | *: 443 | Příchozí |
| 23.99.5.239 | *: 443 | Příchozí |
| 168.61.48.131 | *: 443 | Příchozí |
| 138.91.141.162 | *: 443 | Příchozí |

Přidejte taky IP adresy specifické pro oblast, ve které se cluster vytvoří. Seznam adres pro každou oblast Azure najdete v tématu věnovaném [IP adresám správy HDInsight](../hdinsight-management-ip-addresses.md) .

Pokud používáte Express Route nebo vlastní server DNS, přečtěte si téma [plánování virtuální sítě pro Azure HDInsight – připojení více sítí](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Zámky prostředků  

Zajistěte, aby [ve vaší virtuální síti a skupině prostředků nejsou žádné zámky](../../azure-resource-manager/management/lock-resources.md). Clustery nelze vytvořit ani odstranit, pokud je skupina prostředků uzamčena. 

## <a name="unsupported-component-versions"></a>Nepodporované verze součástí

Ujistěte se, že ve vašem řešení používáte [podporovanou verzi Azure HDInsight a Apache Hadoop komponenty](../hdinsight-component-versioning.md) .  

## <a name="storage-account-name-restrictions"></a>Omezení názvu účtu úložiště

Názvy účtů úložiště nesmí být delší než 24 znaků a nesmí obsahovat speciální znak. Tato omezení platí také pro výchozí název kontejneru v účtu úložiště.

Pro vytvoření clusteru se taky použijí další omezení pojmenování. Další informace najdete v tématu [omezení názvu clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Výpadky služeb

Ověřte [stav Azure](https://status.azure.com) pro případné potenciální výpadky nebo problémy se službami.

## <a name="next-steps"></a>Další kroky

* [Rozšíření služby Azure HDInsight pomocí služby Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
