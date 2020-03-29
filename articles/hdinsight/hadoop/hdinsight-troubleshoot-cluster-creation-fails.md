---
title: Poradce při potížích s chybami při vytváření clusteru pomocí Azure HDInsight
description: Přečtěte si, jak řešit problémy s vytvářením clusterů Apache pro Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 1e13c7ef8eae81ef2a12a8761b0596f6329f94dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937908"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Poradce při potížích s chybami při vytváření clusteru pomocí Azure HDInsight

Následující problémy jsou nejčastější hlavní příčiny selhání vytváření clusteru:

- Problémy s oprávněním
- Omezení zásad zdrojů
- Brány firewall
- Zámky prostředků
- Nepodporované verze součástí
- Omezení názvu účtu úložiště
- Výpadky služeb

## <a name="permissions-issues"></a>Problémy s oprávněními

Pokud používáte Azure Data Lake Storage Gen2 ```AmbariClusterCreationFailedErrorCode``` ```Internal server error occurred while processing the request. Please retry the request or contact support.```a obdržíte chybu , otevřete portál Azure, přejděte na svůj účet úložiště a v části Řízení přístupu (IAM) se ujistěte, že **datový přispěvatel objektu blob úložiště** nebo role Vlastník a **blob úložiště** má přiřazenpřístup **uživateli přiřazené spravované identitě** pro předplatné. Podrobné pokyny najdete v tématu [Nastavení oprávnění pro spravovanou identitu v účtu Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Pokud používáte Azure Data Lake Storage Gen1, přečtěte si pokyny k nastavení a konfiguraci [zde](../hdinsight-hadoop-use-data-lake-store.md). Úložiště datového jezera Gen1 není podporováno pro clustery HBase a není podporováno ve verzi HDInsight verze 4.0.

Pokud používáte Azure Storage, ujistěte se, že název účtu úložiště je platný během vytváření clusteru.

## <a name="resource-policy-restrictions"></a>Omezení zásad zdrojů

Zásady Azure založené na předplatném můžete odmítnout vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.  

Obecně platí, že vytváření clusteru může mít následující zásady vliv:

* Zásady brání vytváření IP adres & vyrovnávání zatížení v rámci předplatného.
* Zásady brání vytvoření účtu úložiště.
* Zásady bráníodstranění síťových prostředků (IP Adresa /Nástroje pro vyrovnávání zatížení).

## <a name="firewalls"></a>Brány firewall

Brány firewall na vašem účtu virtuální sítě nebo úložiště mohou odepřít komunikaci s IP adresami hdinsight u správy.

Povolte provoz z IP adres v následující tabulce.

| Zdrojová IP adresa | Cíl | Směr |
|---|---|---|
| 168.61.49.99 | *:443 | Příchozí |
| 23.99.5.239 | *:443 | Příchozí |
| 168.61.48.131 | *:443 | Příchozí |
| 138.91.141.162 | *:443 | Příchozí |

Přidejte také adresy IP specifické pro oblast, kde je cluster vytvořen. Seznam adres pro každou oblast Azure najdete v [tématu IP adresy pro správu HDInsight.](../hdinsight-management-ip-addresses.md)

Pokud používáte expresní trasu nebo vlastní dns server, přečtěte si informace [o plánování virtuální sítě pro Azure HDInsight – připojení více sítí](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Uzamčení prostředků  

Ujistěte se, že ve [vaší virtuální síti a skupině prostředků](../../azure-resource-manager/management/lock-resources.md)nejsou žádné zámky . Clustery nelze vytvořit ani odstranit, pokud je skupina prostředků uzamčena. 

## <a name="unsupported-component-versions"></a>Nepodporované verze součástí

Ujistěte se, že používáte [podporovanou verzi Azure HDInsight](../hdinsight-component-versioning.md) a všechny [součásti Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) ve vašem řešení.  

## <a name="storage-account-name-restrictions"></a>Omezení názvu účtu úložiště

Názvy účtů úložiště nesmí mít více než 24 znaků a nemohou obsahovat speciální znak. Tato omezení platí také pro výchozí název kontejneru v účtu úložiště.

Pro vytvoření clusteru platí také další omezení pojmenování. Další informace naleznete [v tématu Omezení názvů clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Výpadky služeb

Zkontrolujte [stav Azure](https://status.azure.com) pro případné výpadky nebo problémy se službami.

## <a name="next-steps"></a>Další kroky

* [Rozšíření služby Azure HDInsight pomocí služby Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
