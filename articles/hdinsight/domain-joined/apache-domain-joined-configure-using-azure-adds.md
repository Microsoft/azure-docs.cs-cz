---
title: Konfigurace clusterů HDInsight připojený k doméně pomocí AAD DS
description: Zjistěte, jak připravit a nakonfigurovat clusterů HDInsight připojený k doméně pomocí nástroje Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849822"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurace clusterů HDInsight připojený k doméně pomocí nástroje Azure Active Directory Domain Services

Připojené k doméně clustery poskytovat přístup k více uživateli v clusterech prostředí HDInsight. Clustery HDInsight připojený k doméně jsou připojené do domény, tak, aby uživatelé domény můžete používat přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku zjistěte, jak nakonfigurovat cluster HDInsight připojený k doméně pomocí Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Vytvoření Azure přidá

Povolení služby Azure AD Domain Services (AAD-DS) je požadována, abyste mohli vytvořit cluster HDInsight připojený k doméně. Povolit instanci služby AAD DS, najdete v části [povolení AAD-DS pomocí portálu Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Pouze správci klientů mají oprávnění k vytvoření instance služby AAD DS. Pokud používáte Azure Data Lake Storage (ADLS) jako výchozí úložiště pro HDInsight, pak zajistěte, aby že výchozí klient Azure AD pro ADLS je stejná jako doména clusteru HDInsight. Sincde Hadoop závisí na protokolu Kerberos a základní ověřování, služba Multi-Factor authentication musí být zakázána pro uživatele, kteří budou mít přístup ke clusteru.

Po zřízená instance AAD DS, musíte vytvořit účet služby v AAD (která se budou synchronizovat s AAD DS) s potřebnými oprávněními. Pokud tento účet služby již existuje, budete muset obnovit své heslo a počkejte, dokud jej synchronizuje se do služby AAD DS (Tato resetování bude mít za následek vytvoření hodnoty hash hesla pomocí protokolu kerberos a může to trvat až 30 min pro synchronizaci služby AAD DS). Tento účet služby musí mít následující oprávnění:

- Připojení počítače k doméně a umístěte objekty počítače v rámci organizační jednotky, které zadáte během vytváření clusteru.
- Vytvořte objekty služby v rámci organizační jednotky, které zadáte během vytváření clusteru.

> [!NOTE]
> Protože Apache Zeppelin používá název domény k ověření účtu správce služby, musíte mít účet služby se stejným názvem domény jako jeho přípona UPN pro Apache Zeppelin fungovat správně.

Další informace o organizačních jednotkách a způsobu jejich správy najdete v tématu [vytvořit organizační jednotku v AAD služby DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Zabezpečený LDAP pro AAD DS spravované domény je povinný. Chcete-li povolit zabezpečený LDAP, přečtěte si téma [konfiguraci zabezpečený LDAP (LDAPS) pro AAD služby DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Vytvoření clusteru HDInsight se připojený k doméně

Dalším krokem je vytvoření clusteru HDInsight pomocí služby DS AAD a účet služby vytvořené v předchozí části.

Je jednodušší umístit ve stejném Azure virtuální network(VNet) AAD-DS i clusteru HDInsight. V případě, že jste se rozhodli jejich umístění v různých virtuálních sítí, musí se tak, aby virtuální počítače HDI směrem pohledu na řadič domény pro připojení virtuálních počítačů peer tyto virtuální sítě. Další informace najdete v tématu [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md).

Když vytvoříte cluster HDInsight připojený k doméně, je nutné zadat následující parametry:

- **Název domény**: název domény přidružený k AAD DS. Například contoso.onmicrosoft.com
- **Uživatelské jméno domény**: účet služby v spravované domény, který je vytvořen v předchozí části. Například, hdiadmin@contoso.onmicrosoft.com. Tento uživatel domény, bude správce tento cluster HDInsight.
- **Heslo domény**: heslo účtu služby.
- **Organizační jednotka**: rozlišující název organizační jednotky, kterou chcete použít s clusterem HDInsight. Například: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Pokud tuto organizační jednotku neexistuje, pokusí se HDInsight cluster vytvářet organizační jednotku pomocí oprávnění, které má účet služby. (Například pokud je účet služby AAD DS skupiny Administrators, má správná oprávnění k vytvoření organizační jednotky, v opačném případě může být nutné nejprve vytvořit organizační jednotku a zadejte účet služby, které plnou kontrolu nad dané organizační jednotce nejprve – Viz [vytvořit organizační jednotku na AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)).

    > [!IMPORTANT]
    > Je důležité zahrnout všechny řadiče domény sepearated čárkou po organizační jednotky (například OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com).

- **Adresa URL LDAPS**: například ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Zadejte úplnou adresu url včetně ldaps: / / a číslo portu (: 636)

- **Skupina uživatelů přístup**: skupiny zabezpečení, jejichž uživatelé, které chcete synchronizovat do clusteru. Například HiveUsers. Pokud chcete zadat víc skupin uživatelů, oddělte je čárkou ','.
 
Následující snímek obrazovky ukazuje konfigurace na portálu Azure:

![Konfigurace Azure HDInsight připojený k doméně Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Další postup
* Pokud chcete konfigurovat zásady Hivu a spouštět dotazy Hivu, přečtěte si téma [Konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md).
* Pro připojení ke clusterům HDInsight připojený k doméně pomocí protokolu SSH, najdete v části [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

