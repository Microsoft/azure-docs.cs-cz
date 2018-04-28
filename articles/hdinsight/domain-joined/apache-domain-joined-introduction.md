---
title: Azure HDInsight - připojený k doméně clustery HDInsight-
description: Zjistěte...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 6225bd824e3bcff24b84c79f39ce209f16caafd8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Úvod do Hadoop zabezpečení s clustery HDInsight připojený k doméně

Služba Azure HDInsight až doteď podporovala jako místního správce pouze jednoho uživatele. To dobře fungovalo pro menší aplikace, týmy nebo oddělení. Jako Hadoop založené na úlohy, které získávají další oblíbenosti v odvětví enterprise potřebu s podnikovými úrovni jako je ověřování na základě služby active directory, podpora více uživatelů a řízení přístupu na základě rolí jsou stále důležité. Pomocí clusterů HDInsight připojených k doméně můžete vytvořit cluster HDInsight připojený k doméně služby Active Directory a nakonfigurovat seznam zaměstnanců podniku, kteří se pro přihlášení ke clusteru mohou ověřovat prostřednictvím služby Azure Active Directory. Nikdo jiný mimo podnik se ke clusteru HDInsight nemůže přihlásit ani připojit. Správce podnikové sítě můžete konfigurovat řízení přístupu na základě role pomocí Hive zabezpečení [Apache škálu](http://hortonworks.com/apache/ranger/), proto omezuje přístup k datům jenom tolik, podle potřeby. Kromě toho může správce také provádět audit přistupování k datům zaměstnanci a jakýchkoli změn provedených v zásadách řízení přístupu. Tím dosáhne vysokého stupně dohledu nad firemními prostředky.

> [!NOTE]
> Nové funkce popsané v tomto článku jsou dostupné pouze pro následující typy clusteru: Hadoop, Spark a interaktivní dotazu. Další úlohy, HBase, Storm a Kafka, bude povolená v budoucích verzích.

> [!IMPORTANT]
> Oozie není povoleno v doméně HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="benefits"></a>Výhody
Podnikové zabezpečení obsahuje čtyři hlavní pilíře – hraniční zabezpečení, ověřování, ověřování a šifrování.

![Pilíře výhod clusterů HDInsight připojených k doméně](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Zabezpečení perimetru
Služba HDInsight zajišťuje zabezpečení perimetru pomocí virtuálních sítí a služby Gateway. Dnes může podnikový správce vytvořit cluster HDInsight uvnitř virtuální sítě a pomocí skupin zabezpečení sítě (příchozí nebo odchozí pravidla brány firewall) omezit přístup k této virtuální síti. Pouze IP adresy definované v příchozích pravidlech brány firewall budou moci komunikovat s clusterem HDInsight a tak je zajištěno zabezpečení perimetru. Další vrstvu zabezpečení perimetru zajišťuje služba Gateway. Brána je službu, která funguje jako první linií obrany pro všechny příchozí požadavek na clusteru HDInsight. Tato služba požadavek přijme, ověří jej a teprve pak mu umožní průchod do dalších uzlů v clusteru. Tím zajišťuje zabezpečení ostatních jmenných a datových uzlů v clusteru.

### <a name="authentication"></a>Authentication
Správce podniku můžete vytvořit cluster HDInsight se připojený k doméně v [virtuální sítě](https://azure.microsoft.com/services/virtual-network/). Uzly clusteru HDInsight budou připojeny k doméně spravované podnikem. Toho můžete dosáhnout s použitím služby [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Všechny uzly v clusteru jsou připojeny k doméně spravované podnikem. S tímto nastavením se mohou zaměstnanci podniku přihlašovat ke clusteru pomocí svých přihlašovacích údajů domény. Přihlašovací údaje domény může také používat k ověřování pro ostatní schválené koncové body, jako je Hue, zobrazení Ambari, rozhraní ODBC, JDBC, prostředí PowerShell a rozhraní REST API pro interakci s clusterem. Správce má úplnou kontrolu nad omezením počtu uživatelů pracujících s clusterem přes tyto koncové body.

### <a name="authorization"></a>Autorizace
Osvědčeným postupem, který dodržuje většina podniků, je, že ne každý zaměstnanec má přístup ke všem podnikovým prostředkům. V této verzi se podobně správce můžete definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. Správce může například nakonfigurovat [Apache Ranger](http://hortonworks.com/apache/ranger/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje, že uživatelů budou mít přístup pouze k nezbytnému množství dat, která potřebují k úspěšné práci. Přístup ke clusteru přes SSH je také omezen pouze pro správce.

### <a name="auditing"></a>Auditování
Kromě ochrany prostředků clusteru HDInsight před neautorizovanými uživateli a zabezpečení dat je nezbytné provádění auditu veškerých přístupů k prostředkům a datům clusteru za účelem sledování neautorizovaného nebo nechtěného přístupu k prostředkům. Správce může zobrazit a sestavy veškerý přístup k prostředkům clusteru HDInsight a datům. Správce může také zobrazit a vytvářet sestavy o všech změnách v zásadách řízení přístupu provedených v koncových bodech podporovaných v Apache Ranger. Cluster HDInsight připojený k doméně používá k prohledávání protokolů auditu známé uživatelské rozhraní Apache Ranger. Na back-endu používá Ranger k ukládání a prohledávání protokolů [Apache Solr](http://hortonworks.com/apache/solr/).

### <a name="encryption"></a>Šifrování
Ochrana dat je důležitá pro splnění požadavků na zabezpečení organizace a dodržování předpisů. Kromě omezení přístupu k datům neautorizovanými uživateli by data měla být zabezpečena také šifrováním. Obě úložiště dat pro clustery HDInsight (Azure Storage Blob a úložiště Azure Data Lake) podporují transparentní [šifrování neaktivních uložených dat](../../storage/common/storage-service-encryption.md) na straně serveru. Zabezpečené clustery HDInsight budou bez problému fungovat s touto schopností šifrování neaktivních uložených dat na straně serveru.

## <a name="next-steps"></a>Další postup
* Pokud chcete konfigurovat cluster HDInsight připojený k doméně, přečtěte si téma [Konfigurace clusterů HDInsight připojených k doméně](apache-domain-joined-configure.md).
* Spravovat cluster HDInsight se připojený k doméně, najdete v části [clustery HDInsight spravovat doméně](apache-domain-joined-manage.md).
* Pokud chcete konfigurovat zásady Hivu a spouštět dotazy Hivu, přečtěte si téma [Konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md).
* Spuštění dotazů Hive pomocí protokolu SSH v clusterech HDInsight připojený k doméně, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Pomocí VSCode propojit clusteru připojené k doméně, najdete v části [propojení k doméně připojený cluster s VSCode](../hdinsight-for-vscode.md#linkcluster).
* Pomocí IntelliJ propojit clusteru připojené k doméně, najdete v části [propojení k doméně připojený cluster s IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#linkcluster).
* Pomocí Eclipse propojit clusteru připojené k doméně, najdete v části [propojení k doméně připojený cluster s Eclipse](../spark/apache-spark-eclipse-tool-plugin.md#linkcluster).
