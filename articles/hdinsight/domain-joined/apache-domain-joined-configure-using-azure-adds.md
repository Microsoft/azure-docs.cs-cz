---
title: Konfigurovat cluster HDInsight připojený k doméně pomocí služby Azure AD DS
description: Zjistěte, jak nastavit a konfigurovat cluster HDInsight připojený k doméně pomocí Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 17924b0a00f4605d41492768b0124c583664aca6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042137"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight připojené k doméně pomocí Azure Active Directory Domain Services

Clustery připojené k doméně zadejte více uživateli přístup v clusterech Azure HDInsight. Clustery HDInsight připojené k doméně jsou připojené k doméně tak, aby uživatelé domény můžete použít své přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku se dozvíte, jak nakonfigurovat cluster HDInsight připojený k doméně pomocí Azure Active Directory Domain Services (Azure AD DS).

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Povolení služby Azure AD DS je požadována, než budete moct vytvořit cluster HDInsight připojený k doméně. Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Pouze správci tenanta máte oprávnění k vytvoření instance služby Azure AD DS. Pokud používáte Azure Data Lake Storage Gen1 jako výchozího úložiště pro HDInsight, ujistěte se, že výchozí tenanta Azure AD pro Data Lake Storage Gen1 je stejná jako doména clusteru HDInsight. Protože Hadoop závisí na protokolu Kerberos a základní ověřování, ověřování službou Multi-Factor Authentication je potřeba zakázat pro uživatele, kteří se přístup ke clusteru.

Jakmile zřídíte instanci služby Azure AD DS, vytvořte účet služby v Azure Active Directory (Azure AD) s příslušným oprávněním. Pokud tento účet už existuje, resetovat heslo a počkejte, dokud ji synchronizuje do služby Azure AD DS. Obnoví způsobí vytvoření hodnoty hash hesla protokolu Kerberos a může trvat až 30 minut pro synchronizaci Azure AD DS. 

Účet služby potřebuje následující oprávnění:

- Připojení počítače k doméně a umístit objekty zabezpečení počítačů v rámci organizační jednotky, které zadáte během vytváření clusteru.
- Vytvoření instančních objektů v rámci organizační jednotky, které zadáte během vytváření clusteru.

> [!NOTE]
> Vzhledem k tomu Apache Zeppelin používá název domény k ověření pomocí účtu správce služby, účet služby *musí* mají stejný název domény jako jeho přípona UPN pro Apache Zeppelin fungovat správně.

Další informace o organizačních jednotkách a jejich správě, naleznete v tématu [vytvoření OU ve spravované doméně Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Protokol Secure LDAP je pro spravované domény služby Azure AD DS. Další informace najdete v tématu [konfigurace zabezpečeného protokolu LDAP pro Azure AD DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Vytvořit cluster HDInsight připojený k doméně

Dalším krokem je vytvoření clusteru HDInsight pomocí Azure AD DS a účet služby, který jste vytvořili v předchozí části.

Je jednodušší umístit instancí Azure AD DS a HDInsight cluster ve stejné virtuální síti Azure. Pokud budete chtít umístit do různých virtuálních sítí, musíte vytvořit partnerský vztah těchto virtuálních sítí, tak, aby virtuální počítače s HDInsight dohled k řadiči domény pro připojení k virtuálním počítačům. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

Když vytvoříte cluster HDInsight připojený k doméně, je nutné zadat následující parametry:

- **Název domény**: název domény, který je spojen s Azure AD DS. Příkladem je contoso.onmicrosoft.com.

- **Uživatelské jméno domény**: účet služby v Azure přidá řadiče domény spravované domény, který jste vytvořili v předchozí části. Příklad: hdiadmin@contoso.onmicrosoft.com. Tento uživatel domény, bude správce tohoto clusteru HDInsight.

- **Heslo domény**: heslo účtu služby.

- **Organizační jednotka**: rozlišující název organizační jednotky, kterou chcete použít s clusterem HDInsight. Příkladem je organizační jednotky = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com. Pokud této organizační jednotky buď neexistuje, pokusí se HDInsight cluster vytvořit organizační jednotku s použitím oprávnění, která má účet služby. Například pokud účet služby je ve skupině Správci služby Azure AD DS, má správná oprávnění k vytvoření organizační jednotky. V opačném případě musíte nejprve vytvořit organizační jednotku a udělit účtu služby, které plnou kontrolu nad dané organizační jednotce. Další informace najdete v tématu [vytvoření OU ve spravované doméně Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Zahrnují všechny řadiče domén, oddělených čárkami, po organizační jednotky (například OU HDInsightOU, DC = contoso, DC = = onmicrosoft, DC = com).

- **LDAPS URL**: příklad je ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Zadejte úplnou adresu URL, včetně "ldaps: / /" a číslo portu (: 636).

- **Přístupová skupina uživatelů**: skupiny zabezpečení uživatelů chcete synchronizovat do clusteru. Například HiveUsers. Pokud chcete zadat víc skupin uživatelů, oddělte je středníkem (;). Tyto skupiny uplatněna musí existovat v adresáři před zřizování. Další informace najdete v tématu [vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Pokud skupina neexistuje, dojde k chybě: "HiveUsers skupiny nebyl nalezen ve službě Active Directory."

Následující snímek obrazovky ukazuje konfigurace na webu Azure Portal:

   ![Konfigurace Azure HDInsight připojené k doméně Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Další postup
* Konfigurace zásad Hivu a spouštět dotazy Hive, najdete v části [konfigurace zásad Hivu pro clustery HDInsight připojené k doméně](apache-domain-joined-run-hive.md).
* Pro připojení ke clusterům HDInsight připojených k doméně pomocí protokolu SSH, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

