---
title: Architektura Azure HDInsight připojený k doméně | Microsoft Docs
description: Naučte se plánovat službu HDInsight připojenou k doméně.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8503534031dc5774e64c58edd3e158162a5a6aee
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110450"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Plánování clusterů Azure Hadoop připojených k doméně ve službě HDInsight

Standardní clusteru HDInsight je cluster s jedním uživatelem. Je vhodný pro většinu společností, které mají menší aplikační týmy sestavující úlohy velkých objemů dat. Každý uživatel může mít jiný cluster vyhrazený pro sám na vyžádání a zničte, jakmile ho už nepotřebují. Mnoho podniků však přesouvá směrem k model, při kterém clustery spravují IT týmy a více aplikací týmy sdílené složky clustery spuštěná. Proto s více uživateli přístup ke clusteru je potřeba v Azure HDInsight pro tyto velkých podnicích.

HDInsight využívá nejoblíbenější zprostředkovatele identity – Active Directory (AD) spravovaných způsobem. Díky integraci s HDInsight [Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md), máte přístup k clustery pomocí přihlašovacích údajů domény. Virtuální počítače v prostředí HDInsight jsou připojené k doméně k zadané doméně, proto všechny služby systémem HDInsight (Ambari, Hive serveru škálu, Spark thrift serveru a dalších) pracovní bezproblémově pro ověřené uživatele. Správci pak mohou vytvářet zásady silné autorizace pomocí Apache škálu k poskytování řízení přístupu na základě rolí pro prostředky v clusteru.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrace služby HDInsight s Active Directory

Open-source Hadoop závisí na protokolu Kerberos pro zajištění ověřování a zabezpečení. Proto jsou uzly clusteru HDInsight domény připojený k doméně spravované službou AAD DS. Zabezpečení protokolu Kerberos je nakonfigurován pro součásti nástroje Hadoop v clusteru. Pro každé součásti Hadoop je automaticky vytvořen objekt služby. Hlavní počítač s odpovídající se také vytvoří pro každý počítač, který je připojený k doméně. Chcete-li uložit těchto objektů služby a počítače, je třeba zajistit organizační jednotce (OU) v řadiči domény (AAD-DS), kde jsou umístěny těchto objektů. 

To Shrneme, musíte nastavit prostředí s:

- Domény služby Active Directory (spravované službou AAD DS)
- Zabezpečení protokolu LDAP (LDAPS) v AAD DS povolené.
- Řádné síťové připojení z prostředí HDInsight pro virtuální síť k virtuální síti AAD DS, v případě, že zvolíte samostatné virtuální sítě pro ně. Virtuální počítač uvnitř virtuální sítě HDI by měl mít směrem pohledu do služby AAD DS pomocí virtuální sítě partnerský vztah. Pokud HDI a AAD DS nasazených ve stejné virtuální síti, připojení se automaticky poskytuje a není vyžadována žádná další akce.
- Organizační jednotce (OU) [na AAD DS vytvořit](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- Účet služby, který má oprávnění pro:
    - Vytvořte objekty služby v organizační jednotce.
    - Připojení počítače k doméně a vytvořit objekty počítače v organizační jednotce.

Následující snímek obrazovky ukazuje organizační jednotce vytvořené v doméně contoso.com. Některé objekty služby a objekty počítače se zobrazí také na snímku obrazovky.

![Organizační jednotky clusterů HDInsight připojené k doméně](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Instalace řadiče domény jiný:
HDInsight aktuálně podporuje jenom AAD DS jako řadič domény hlavním, cluster bude komunikovat s Kerberise clusteru. Ale jiné komplexní AD nastavení jsou také možné, dokud ho vede k povolení služby AAD DS pro HDI přístup.

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: Tato služba poskytuje spravované doméně, která je plně kompatibilní s Windows Server Active Directory. Správa, opravy a monitorování doménou, ve vysoce Available(HA) instalace postará Microsoft. Cluster můžete nasadit bez starostí o správu řadičů domény. Uživatelé, skupiny a hesla jsou synchronizované z Azure Active Directory(AAD) [jednosměrné synchronizaci z AAD do služby AAD DS] povolení uživatelům přihlásit ke clusteru pomocí stejné podnikové přihlašovací údaje. Další informace najdete v tématu [jak nakonfigurovat připojený k doméně HDInsight clusterů pomocí AAD DS](./apache-domain-joined-configure-using-azure-adds.md).
- **Místní AD ani AD na virtuální počítače IaaS**: Pokud máte místní AD ani jiné složitější AD nastavení vaší domény, budete moct synchronizovat těchto identit aad pomocí AD Connect a pak povolte klienta služby AAD DS na tuto Reklamu. Vzhledem k tomu, že Kerberos závisí na hodnot hash hesel, budete muset [povolení synchronizace hodnot hash hesel v AAD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Pokud používáte federační službou AD federation Services (ADFS), můžete volitelně můžete nastavit synchronizace hodnot hash hesel jako záložní případ selhání infrastruktury služby AD FS. Další informace najdete v tématu [povolení synchronizace hodnot hash hesel s AAD Connect sync](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). Pomocí místní AD ani AD na virtuální počítače IaaS samostatně, aniž by AAD a AAD DS není podporovanou konfiguraci pro připojení k doméně clusteru HDI.

## <a name="next-steps"></a>Další postup
* [Konfigurovat připojený k doméně clusterů HDInsight](apache-domain-joined-configure-using-azure-adds.md).
* [Nakonfigurovat zásady Hive pro připojené k doméně clustery služby HDInsight](apache-domain-joined-run-hive.md).
* [Správa clusterů HDInsight připojený k doméně](apache-domain-joined-manage.md). 
