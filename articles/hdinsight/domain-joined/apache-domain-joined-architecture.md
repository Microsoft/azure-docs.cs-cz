---
title: Architektura služby Azure HDInsight s balíčkem Enterprise Security Package
description: Informace o plánování zabezpečení HDInsight s balíčkem Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 50c5838f576b6fd6775373f2dbe3c46d751545c1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437584"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Použít balíček zabezpečení podniku v HDInsight

Standardní cluster Azure HDInsight je Jednouživatelský cluster. Je vhodný pro většinu společností, které mají menší aplikační týmy sestavující úlohy velkých objemů dat. Každý uživatel můžete vytvořit vyhrazenou clusteru na vyžádání a zničit ho, když ho už nepotřebují. 

Řada podniků přešli k modelu, kdy clustery spravují IT týmy a sdílí je několik aplikačních týmů. Tyto větší podniky potřebují více uživateli přístup na každý cluster v Azure HDInsight.

HDInsight spoléhá na zprostředkovatele oblíbených identity – Active Directory – spravované způsobem. Díky integraci služby HDInsight s [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), dostanete clustery pomocí vašich přihlašovacích údajů domény. 

Virtuální počítače (VM) v HDInsight jsou domény připojené k zadané doméně. Ano všechny služby spuštěné v HDInsight (Apache Ambari, server Apache Hive, Apache Ranger, server Apache Spark thrift a další) fungují pro ověřeného uživatele. Správci pak mohou vytvářet zásady silné ověřování s použitím Apache Ranger k poskytování řízení přístupu na základě rolí pro prostředky v clusteru.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrace služby HDInsight s Active Directory

Open source Apache Hadoop, závisí na protokolu Kerberos pro ověřování a zabezpečení. Proto jsou uzly clusteru HDInsight s Enterprise Security Package (ESP) připojený k doméně, který je spravovaný službou Azure AD DS. Zabezpečení protokolu Kerberos je nakonfigurován pro součásti platformy Hadoop v clusteru. 

Automaticky vytvoří následující věci:
- objekt služby pro jednotlivé komponenty systému Hadoop 
- objekt počítače pro každý počítač, který je připojený k doméně
- Organizační jednotce (OU) pro každý cluster pro uchovávání objektů tyto služby a počítače 

Souhrnně řečeno, musíte nastavit prostředí pomocí:

- Domény služby Active Directory (spravované službou Azure AD DS).
- Zabezpečený LDAP (LDAPS) povolena ve službě Azure AD DS.
- Správné síťové připojení z virtuální sítě HDInsight do Azure AD DS virtuální sítě, pokud se rozhodnete pro ně samostatné virtuální sítě. Virtuální počítač ve virtuální síti HDInsight by měl mít dohled do služby Azure AD DS prostřednictvím partnerského vztahu virtuální sítě. Pokud HDInsight a Azure AD DS jsou nasazené ve stejné virtuální síti, připojení se automaticky k dispozici a není potřeba žádná další akce.

## <a name="set-up-different-domain-controllers"></a>Nastavit různými řadiči domény
HDInsight aktuálně podporuje pouze Azure AD DS jako řadič domény hlavním cluster používá pro komunikaci pomocí protokolu Kerberos. Ale jiné komplexní nastavení služby Active Directory je to možné, tak dlouho, dokud taková konfigurace vede k povolení služby Azure AD DS pro přístup k HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) poskytuje spravovanou doménu, která je plně kompatibilní s Windows Server Active Directory. Microsoft se postará o správu, použití dílčích oprav a sledování domény v s vysokou dostupností (HA) nastavení. Cluster můžete nasadit bez starostí o údržbu řadičů domény. 

Uživatelé, skupiny a hesla jsou synchronizovaná z Azure Active Directory (Azure AD). Jednosměrné synchronizaci z vaší instance služby Azure AD do služby Azure AD DS umožňuje uživatelům přihlášení ke clusteru pomocí stejné podnikové přihlašovací údaje. 

Další informace najdete v tématu [konfigurace HDInsight clustery s využitím Azure AD DS ESP](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>V místní službě Active Directory nebo Active Directory na virtuálních počítačích IaaS

Pokud máte složitější nastavení služby Active Directory nebo instance místní služby Active Directory pro vaši doménu, můžete tyto identity do služby Azure AD synchronizovat s použitím služby Azure AD Connect. Pak můžete povolit Azure AD DS na tohoto tenanta Active Directory. 

Protože protokol Kerberos závisí na hodnot hash hesel, budete muset [povolení synchronizace hodnot hash hesel ve službě Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Pokud používáte federace s Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizace hodnot hash hesel jako záložní v případě selhání infrastruktury služby AD FS. Další informace najdete v tématu [povolení synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Pomocí místní služby Active Directory nebo Active Directory na virtuálních počítačích IaaS samostatně, bez Azure AD a Azure AD DS, není podporované konfigurace pro clustery HDInsight se ESP.

## <a name="next-steps"></a>Další postup

* [Konfigurace clusterů HDInsight s ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurace zásad Apache Hivu pro clustery HDInsight s ESP](apache-domain-joined-run-hive.md)
* [Správa clusterů HDInsight s ESP](apache-domain-joined-manage.md) 
