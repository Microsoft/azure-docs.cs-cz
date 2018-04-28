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
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 20d6dbad6fa1914c8b12f47bb48f6efba3895887
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Plánování clusterů Azure Hadoop připojených k doméně ve službě HDInsight

Standardní clusteru HDInsight je cluster s jedním uživatelem. Je vhodný pro většinu společností, které mají menší aplikační týmy sestavující úlohy velkých objemů dat. Jako Hadoop získávají oblíbenosti, spustit mnoho podniků přesouvá směrem k model, při kterém clustery spravují IT týmy a více aplikací týmy clustery sdílené složky. Proto jsou funkce zahrnující víceuživatelské clustery mezi nejžádanějšími funkcemi ve službě Azure HDInsight.

Místo vytváření vlastní víceuživatelská ověřování a autorizaci, HDInsight využívá nejoblíbenější zprostředkovatele identity – Active Directory (AD). Funkci efektivní zabezpečení ve službě Active Directory lze použít ke správě více uživatelů ověřování v HDInsight. Integrací HDInsight s AD může komunikovat s clustery pomocí svých přihlašovacích údajů AD. Virtuální počítače v prostředí HDInsight jsou připojené k doméně do služby AD, a jak HDInsight mapuje Active Directory na místní uživatel Hadoop, takže všechny služby spuštěné v HDInsight (Ambari, Hive serveru škálu, Spark thrift serveru a dalších) pracovní bezproblémově pro ověřené uživatele. Správce potom můžete vytvořit zásady silné autorizace pomocí Apache škálu k poskytování řízení přístupu na základě rolí pro prostředky v HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrace služby HDInsight s Active Directory

Při integraci HDInsight se službou Active Directory, jsou uzly clusteru HDInsight domény připojený k doméně AD. Zabezpečení protokolu Kerberos je nakonfigurován pro součásti nástroje Hadoop v clusteru. Pro všechny komponenty Hadoop je vytvořen objekt služby na službě Active Directory. Hlavní počítač s odpovídající se také vytvoří pro každý počítač, který je připojený k doméně. Tyto objekty služby a objekty počítače můžete zbytečných souborů služby Active Directory. V důsledku toho je třeba zajistit organizační jednotce (OU) v rámci služby Active Directory, kde jsou umístěny těchto objektů. 

To Shrneme, je nutné nastavit prostředí s:

- Řadič domény služby Active Directory s LDAPS nakonfigurované.
- Připojení z virtuální sítě na HDInsight k řadiči domény služby Active Directory.
- Na službě Active Directory vytvořit organizační jednotku.
- Účet služby, který má oprávnění pro:

    - Vytvořte objekty služby v organizační jednotce.
    - Připojení počítače k doméně a vytvořit objekty počítače v organizační jednotce.

Následující snímek obrazovky ukazuje organizační jednotce vytvořené v doméně contoso.com. Některé objekty služby a objekty počítače se zobrazí také na snímku obrazovky.

![Organizační jednotky clusterů HDInsight připojené k doméně](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="the-way-of-bringing-your-own-active-directory-domain-controllers"></a>Způsob uvedení vlastní řadiče domény služby Active Directory

- **Azure Active Directory Domain Services**: Tato služba poskytuje spravované doméně služby Active Directory, která je plně kompatibilní s Windows Server Active Directory. Microsoft má na starosti správu, opravy a monitorování doméně AD. Cluster můžete nasadit bez starostí o správu řadičů domény. Uživatelé, skupiny a hesla se synchronizují ze služby Azure Active Directory, uživatelé budou moct přihlásit ke clusteru pomocí své podnikové přihlašovací údaje. Další informace najdete v tématu [clusterů HDInsight připojený k doméně nakonfigurovat pomocí Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).


## <a name="next-steps"></a>Další postup
* Pokud chcete spravovat clustery HDInsight připojené k doméně, přečtěte si téma [Správa clusterů HDInsight připojených k doméně](apache-domain-joined-manage.md).
* Pokud chcete konfigurovat zásady Hivu a spouštět dotazy Hivu, přečtěte si téma [Konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md).
* Ke spouštění dotazů Hive pomocí protokolu SSH na doméně clusterů HDInsight, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
