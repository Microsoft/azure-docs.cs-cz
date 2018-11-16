---
title: Úvod do Apache Hadoop zabezpečení s balíčkem Enterprise Security Package
description: Zjistěte, jak Enterprise Security Package podporuje čtyři pilíře podnikového zabezpečení.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 270227204af9cf50f0244b8aa11ebf9aa8cdc3ce
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51631995"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Úvod do Apache Hadoop zabezpečení s balíčkem Enterprise Security Package

V minulosti, Azure HDInsight podporován pouze u jednoho uživatele: místní správce. To dobře fungovalo pro menší aplikace, týmy nebo oddělení. Jak se úlohy založené na Apache Hadoopu oblíbenějšími v odvětví enterprise, potřeba pro podporu funkce na podnikové úrovni, jako je ověřování na základě služby Active Directory, více uživatelů a řízení přístupu na základě rolí stal nabývá na důležitosti. 

Můžete vytvořit cluster služby HDInsight s Enterprise Security Package (ESP), který je připojený k doméně služby Active Directory. Potom můžete nakonfigurovat seznam zaměstnanců podniku, kteří mohou ověřovat prostřednictvím služby Azure Active Directory pro přihlášení ke clusteru HDInsight. Nikdo z mimo organizaci můžete přihlásit nebo přístup ke clusteru HDInsight. 

Podnikový správce můžete konfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení Apache Hive pomocí [Apache Ranger](http://hortonworks.com/apache/ranger/). Konfigurace RBAC omezí přístup k datům jenom to, co je potřeba. Nakonec správce můžete auditovat přístup k datům zaměstnanci a jakýchkoli změn provedených v zásadách řízení přístupu. Správce pak může dosáhnout vysokého stupně dohledu nad firemními prostředky.

> [!NOTE]
> Oozie je nyní zapnuta ESP clustery. Pro přístup k webovým Uživatelským rozhraním Oozie, musí uživatelé povolit [tunelování](../hdinsight-linux-ambari-ssh-tunnel.md).

Podnikové zabezpečení obsahuje čtyři hlavní pilíře: zabezpečení perimetru, ověřování, autorizace a šifrování.

![Výhody clustery HDInsight balíček zabezpečení podniku v čtyři pilíře podnikové zabezpečení](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Zabezpečení perimetru
Zabezpečení perimetru v HDInsight se dosahuje prostřednictvím virtuálních sítí a službou Azure VPN Gateway. Může podnikový správce můžete vytvořit cluster ESP uvnitř virtuální sítě a pomocí skupin zabezpečení sítě (pravidla brány firewall) omezit přístup k virtuální síti. Pouze IP adresy definované v pravidlech brány firewall pro příchozí budou moci komunikovat s clusterem HDInsight. Tato konfigurace zajišťuje zabezpečení perimetru.

Další vrstvu zabezpečení perimetru zajišťuje přes službu VPN Gateway. Brána funguje jako první linie obrany pro veškeré příchozí požadavky do clusteru HDInsight. Tato služba požadavek přijme, ověří jej a teprve pak mu umožní průchod do dalších uzlů v clusteru. Tímto způsobem brána poskytuje zabezpečení perimetru do jiných jmenných a datových uzlů v clusteru.

## <a name="authentication"></a>Authentication
Může podnikový správce vytvořit HDInsight cluster s ESP v [virtuální sítě](https://azure.microsoft.com/services/virtual-network/). Všechny uzly clusteru HDInsight jsou připojené k doméně, která spravuje podnik. Toho můžete dosáhnout prostřednictvím [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

S tímto nastavením zaměstnanci podniku může přihlásit k uzlům clusteru s použitím svých přihlašovacích údajů domény. Lze také použít svoje přihlašovací údaje domény k ověření dalších schválených koncových bodech jako zobrazení Ambari ODBC, JDBC, PowerShell a rozhraní REST API pro interakci s clusterem. Správce má plnou kontrolu nad omezením počtu uživatelů, kteří pracují s clusterem přes tyto koncové body.

## <a name="authorization"></a>Autorizace
Osvědčeným postupem, které následují většina podniků, je zajistit, že ne každý zaměstnanec má přístup ke všem podnikovým prostředkům. Správce, můžete definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. 

Správce může například nakonfigurovat [Apache Ranger](http://hortonworks.com/apache/ranger/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje, aby zaměstnanci měli přístup ke pouze tolik dat, jako je třeba je úspěšné práci. Přístup přes SSH ke clusteru je také omezen na pouze správce.

## <a name="auditing"></a>Auditování
Auditu veškerých přístupů k prostředkům clusteru a data, je nezbytné ke sledování neautorizovaného nebo nechtěného přístupu k prostředkům. Je důležité jako Ochrana prostředků clusteru HDInsight před neoprávněnými uživateli a zabezpečení dat. 

Správce můžete zobrazit a sestavy o všech přístupech k prostředkům clusteru HDInsight a data. Správce můžete také zobrazit a sestavy o všech změnách v zásadách řízení přístupu, který je vytvořen v Apache Ranger podporované koncové body. 

Cluster HDInsight s ESP používá k prohledávání protokolů auditu známé uživatelské rozhraní Apache Ranger. V rámci back-endu používá Ranger [Apache Solr](http://hortonworks.com/apache/solr/) pro ukládání a prohledávání protokolů.

## <a name="encryption"></a>Šifrování
Ochrana dat je důležitá pro organizační požadavky zabezpečení a dodržování předpisů na schůzku. Kromě omezení přístupu k datům neautorizovanými, by ho zašifrovat. 

Obě úložiště dat pro clustery HDInsight – Azure Blob storage a Azure Data Lake Storage Gen1 – podpora transparentní serverové [šifrování dat](../../storage/common/storage-service-encryption.md) v klidovém stavu. Zabezpečené clustery HDInsight budou bez problému fungovat díky této funkci šifrování na straně serveru dat v klidovém stavu.

## <a name="next-steps"></a>Další postup

* [Plán pro clustery HDInsight se ESP](apache-domain-joined-architecture.md)
* [Konfigurace clusterů HDInsight s ESP](apache-domain-joined-configure.md)
* [Správa clusterů HDInsight s ESP](apache-domain-joined-manage.md)
* [Konfigurace zásad Hivu pro clustery HDInsight s ESP](apache-domain-joined-run-hive.md)
* [Použití SSH s HDInsightem](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

