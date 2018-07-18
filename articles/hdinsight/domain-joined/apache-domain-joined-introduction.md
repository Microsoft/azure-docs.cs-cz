---
title: Úvod do zabezpečení Hadoop s clustery Azure HDInsight připojené k doméně
description: Zjistěte, jak připojených k doméně Azure HDInsight clustery podporují čtyři pilíře podnikového zabezpečení.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 6f2c41aff8aaa389a8f2288cbb445e1ba2e7fd14
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112532"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Úvod do zabezpečení Hadoop s clustery HDInsight připojené k doméně

V minulosti, Azure HDInsight podporován pouze u jednoho uživatele: místní správce. To dobře fungovalo pro menší aplikace, týmy nebo oddělení. Jak se úlohy založené na Hadoopu oblíbenějšími v odvětví enterprise, potřeba pro podporu funkce na podnikové úrovni, jako je ověřování na základě služby Active Directory, více uživateli, a řízení přístupu na základě rolí stal nabývá na důležitosti. 

Můžete vytvořit cluster HDInsight, který je připojený k doméně služby Active Directory. Potom můžete nakonfigurovat seznam zaměstnanců podniku, kteří mohou ověřovat prostřednictvím služby Azure Active Directory pro přihlášení ke clusteru HDInsight. Nikdo jiný mimo podnik nemůže přihlásit nebo přístup ke clusteru HDInsight. 

Podnikový správce můžete konfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení Hivu pomocí [Apache Ranger](http://hortonworks.com/apache/ranger/). Konfigurace RBAC omezí přístup k datům jenom to, co je potřeba. Nakonec správce můžete auditovat přístup k datům zaměstnanci a jakýchkoli změn provedených v zásadách řízení přístupu. Správce pak může dosáhnout vysokého stupně dohledu nad firemními prostředky.

> [!NOTE]
> Nové funkce popsané v tomto článku jsou dostupné ve verzi preview pouze následující typy clusteru: Hadoop, Spark a interaktivní dotaz. Oozie je nyní zapnuta clustery připojené k doméně. Pro přístup k webovým Uživatelským rozhraním Oozie, musí uživatelé povolit [tunelování](../hdinsight-linux-ambari-ssh-tunnel.md).

Podnikové zabezpečení obsahuje čtyři hlavní pilíře: zabezpečení perimetru, ověřování, autorizace a šifrování.

![Výhod clusterů HDInsight připojených k doméně v čtyři pilíře podnikové zabezpečení](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Zabezpečení perimetru
Zabezpečení perimetru v HDInsight se dosahuje prostřednictvím virtuálních sítí a službou Azure VPN Gateway. Může podnikový správce můžete vytvořit cluster HDInsight uvnitř virtuální sítě a pomocí skupin zabezpečení sítě (pravidla brány firewall) omezit přístup k virtuální síti. Pouze IP adresy definované v pravidlech brány firewall pro příchozí budou moci komunikovat s clusterem HDInsight. Tato konfigurace zajišťuje zabezpečení perimetru.

Další vrstvu zabezpečení perimetru zajišťuje přes službu VPN Gateway. Brána funguje jako první linie obrany pro veškeré příchozí požadavky do clusteru HDInsight. Tato služba požadavek přijme, ověří jej a teprve pak mu umožní průchod do dalších uzlů v clusteru. Tímto způsobem brána poskytuje zabezpečení perimetru do jiných jmenných a datových uzlů v clusteru.

## <a name="authentication"></a>Authentication
Může podnikový správce vytvořit cluster HDInsight připojený k doméně ve [virtuální sítě](https://azure.microsoft.com/services/virtual-network/). Všechny uzly clusteru HDInsight jsou připojené k doméně, která spravuje podnik. Toho můžete dosáhnout prostřednictvím [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

S tímto nastavením zaměstnanci podniku může přihlásit k uzlům clusteru s použitím svých přihlašovacích údajů domény. Lze také použít svoje přihlašovací údaje domény k ověření dalších schválených koncových bodech jako zobrazení Ambari ODBC, JDBC, PowerShell a rozhraní REST API pro interakci s clusterem. Správce má plnou kontrolu nad omezením počtu uživatelů, kteří pracují s clusterem přes tyto koncové body.

## <a name="authorization"></a>Autorizace
Osvědčeným postupem, které následují většina podniků, je zajistit, že ne každý zaměstnanec má přístup ke všem podnikovým prostředkům. Správce, můžete definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. 

Správce může například nakonfigurovat [Apache Ranger](http://hortonworks.com/apache/ranger/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje, aby zaměstnanci měli přístup ke pouze tolik dat, jako je třeba je úspěšné práci. Přístup přes SSH ke clusteru je také omezen na pouze správce.

## <a name="auditing"></a>Auditování
Auditu veškerých přístupů k prostředkům clusteru a data, je nezbytné ke sledování neautorizovaného nebo nechtěného přístupu k prostředkům. Je důležité jako Ochrana prostředků clusteru HDInsight před neoprávněnými uživateli a zabezpečení dat. 

Správce můžete zobrazit a sestavy o všech přístupech k prostředkům clusteru HDInsight a data. Správce můžete také zobrazit a sestavy o všech změnách v zásadách řízení přístupu, který je vytvořen v Apache Ranger podporované koncové body. 

Cluster HDInsight připojený k doméně používá k prohledávání protokolů auditu známé uživatelské rozhraní Apache Ranger. V rámci back-endu používá Ranger [Apache Solr](http://hortonworks.com/apache/solr/) pro ukládání a prohledávání protokolů.

## <a name="encryption"></a>Šifrování
Ochrana dat je důležitá pro organizační požadavky zabezpečení a dodržování předpisů na schůzku. Kromě omezení přístupu k datům neautorizovanými, by ho zašifrovat. 

Obě úložiště dat pro clustery HDInsight – Azure Blob storage a Azure Data Lake Storage Gen1 – podpora transparentní serverové [šifrování dat](../../storage/common/storage-service-encryption.md) v klidovém stavu. Zabezpečené clustery HDInsight budou bez problému fungovat díky této funkci šifrování na straně serveru dat v klidovém stavu.

## <a name="next-steps"></a>Další postup
* [Plán pro clustery HDInsight připojené k doméně](apache-domain-joined-architecture.md)
* [Konfigurace clusterů HDInsight připojených k doméně](apache-domain-joined-configure.md)
* [Správa clusterů HDInsight připojených k doméně](apache-domain-joined-manage.md)
* [Konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md)
* [Použití SSH s HDInsightem](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

