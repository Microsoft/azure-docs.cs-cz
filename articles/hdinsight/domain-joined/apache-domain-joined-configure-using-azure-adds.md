---
title: Konfigurace clusterů HDInsight připojený k doméně pomocí AAD DS
description: Zjistěte, jak připravit a nakonfigurovat clusterů HDInsight připojený k doméně pomocí nástroje Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 060ca8040f514ec1df48c2ca4568cbbb2a529267
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurace clusterů HDInsight připojený k doméně pomocí nástroje Azure Active Directory Domain Services

Připojené k doméně clustery poskytují více uživateli enterprise funkcích zabezpečení v HDInsight. Clustery HDInsight připojený k doméně jsou připojené k domén služby active directory tak, aby uživatelé domény můžete používat přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku zjistěte, jak nakonfigurovat cluster HDInsight připojený k doméně pomocí Azure Active Directory Domain Services.

> [!NOTE]
> Služba Active Directory v virtuální počítače Azure IaaS již není podporována.

## <a name="create-azure-adds"></a>Vytvoření Azure přidá

Budete muset vytvořit Azure AD DS před vytvořením clusteru služby HDInsight. Vytvoření služby Azure přidá naleznete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Pouze správci klientů mají oprávnění k vytvoření služby domain services. Pokud používáte Azure Data Lake Storage (ADLS) jako výchozí úložiště pro HDInsight, pak zajistěte, aby že výchozí klient Azure AD pro ADLS je stejná jako doména clusteru HDInsight. Pro tento nastavený pro práci s Azure Data Lake Store musí být zakázána pro uživatele, kteří budou mít přístup ke clusteru služby Multi-Factor authentication.

Po zřídil službu AAD domény musíte vytvořit účet služby v AAD (která se budou synchronizovat s AAD DS) s potřebnými oprávněními k vytvoření clusteru HDInsight. Pokud tento účet služby již existuje, budete muset resetovat heslo jeho a počkejte, dokud jej synchronizuje se do služby AAD DS (Tato resetování bude mít za následek vytvoření hodnoty hash hesla pomocí protokolu kerberos a může to trvat až 30 minut). Tento účet služby musí mít následující privillages:

- Připojení počítače k doméně a umístěte objekty počítače v rámci organizační jednotky, které zadáte během vytváření clusteru.
- Vytvořte objekty služby v rámci organizační jednotky, které zadáte během vytváření clusteru.

Je nutné povolit zabezpečené LDAP pro Azure AD Domain Services spravované domény. Chcete-li povolit zabezpečený LDAP, přečtěte si téma [konfigurovat zabezpečený LDAP (LDAPS) pro Azure AD Domain Services spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Vytvoření clusteru HDInsight se připojený k doméně

Dalším krokem je vytvoření clusteru HDInsight pomocí AAD DS a účet služby vytvořené v předchozí části.

Je jednodušší umístit ve stejném Azure virtuální network(VNet) služba Azure AD domain Services a clusteru HDInsight. V případě, že jsou v různých virtuálních sítí, musí peer obě virtuální sítě. Další informace najdete v tématu [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md).

Když vytvoříte cluster HDInsight připojený k doméně, je nutné zadat následující parametry:

- **Název domény**: název domény přidružený k Azure AD DS. Například contoso.onmicrosoft.com
- **Uživatelské jméno domény**: účet služby v Azure AD řadič domény, který je vytvořen v předchozí části. Například, hdiadmin@contoso.onmicrosoft.com. Tento uživatel domény, bude správce tento cluster HDInsight připojený k doméně.
- **Heslo domény**: heslo účtu služby.
- **Organizační jednotka**: rozlišující název organizační jednotky, kterou chcete použít s clusterem HDInsight. Například: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Pokud tuto organizační jednotku neexistuje, pokusí se HDInsight cluster vytvořit tuto organizační jednotku. 
- **Adresa URL LDAPS**: například ldaps://contoso.onmicrosoft.com:636
- **Skupina uživatelů přístup**: skupiny zabezpečení, jejichž uživatelé, které chcete synchronizovat do clusteru. Například HiveUsers. Pokud chcete zadat víc skupin uživatelů, oddělte je čárkou ','.
 
> [!NOTE]
> Protože Apache Zeppelin používá název domény k ověření účtu správce služby, musíte mít účet služby se stejným názvem domény jako jeho přípona UPN pro Apache Zeppelin fungovat správně.
 
Následující snímek obrazovky ukazuje konfigurace na portálu Azure:

![Konfigurace Azure HDInsight připojený k doméně Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Další postup
* Pokud chcete konfigurovat zásady Hivu a spouštět dotazy Hivu, přečtěte si téma [Konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md).
* Pro připojení ke clusterům HDInsight připojený k doméně pomocí protokolu SSH, najdete v části [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

