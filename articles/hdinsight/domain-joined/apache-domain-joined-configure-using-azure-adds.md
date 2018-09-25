---
title: Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí služby Azure AD – DS
description: Zjistěte, jak nastavit a konfigurovat cluster HDInsight Enterprise Security Package pomocí Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968369"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí Azure Active Directory Domain Services

Clustery Enterprise Security Package (ESP) poskytují přístup k více uživatelů v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojené k doméně tak, aby uživatelé domény můžete použít své přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku se dozvíte, jak konfigurace clusteru HDInsight s ESP pomocí Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP je k dispozici ve službě Hdinsight 3.6 + pro Hadoop, Spark a interaktivní. ESP pro typy clusterů HBase je ve verzi preview.


## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Povolení služby Azure AD – DS je předpokladem předtím, než vytvoříte HDInsight cluster s ESP. Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Pouze správci tenanta máte oprávnění k vytvoření instance služby Azure AD – DS. Pokud používáte Azure Data Lake Storage Gen1 jako výchozího úložiště pro HDInsight, ujistěte se, že výchozí tenanta Azure AD pro Data Lake Storage Gen1 je stejná jako doména clusteru HDInsight. Protože Hadoop závisí na protokolu Kerberos a základní ověřování, ověřování službou Multi-Factor Authentication je potřeba zakázat pro uživatele, kteří se přístup ke clusteru.

Pro spravované domény služby Azure AD – DS se protokol Secure LDAP. Při povolení protokolu LDAPS, vložte název domény do názvu subjektu nebo v alternativním názvu subjektu v certifikátu. Další informace najdete v tématu [konfigurace zabezpečeného protokolu LDAP pro Azure AD – DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Přidat spravované identity

Po povolení služby Azure AD – DS vytvořit spravovanou identitu a přiřaďte ho **Přispěvatel služby HDInsight domény** roli řízení přístupu Azure AD DS.

![Azure Active Directory domény služby Access control](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Další informace najdete v tématu [co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s ESP

Dalším krokem je vytvoření clusteru HDInsight s ESP aktivuje pomocí služby Azure AD – DS.

Je jednodušší umístit instance služby Azure AD – DS a HDInsight cluster ve stejné virtuální síti Azure. Pokud budete chtít umístit do různých virtuálních sítí, musíte vytvořit partnerský vztah těchto virtuálních sítí, tak, aby virtuální počítače s HDInsight dohled k řadiči domény pro připojení k virtuálním počítačům. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

Při vytváření clusteru služby HDInsight, máte možnost povolit Enterprise Security Package na cluster můžete propojit s Azure AD – DS. 

![Zabezpečení služby Azure HDInsight a sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Jakmile povolíte ESP, časté nesprávné konfigurace související s Azure AD – DS bude automaticky zjistil a ověřit.

![Ověření zabezpečení domény balíčku Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Včasnou detekci šetří čas tím, že vás nutí opravovat chyby před vytvořením clusteru.

![Azure HDInsight balíčkem Enterprise security package se nezdařilo ověření domény](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Při vytváření clusteru HDInsight s ESP, je nutné zadat následující parametry:

- **Uživatel s rolí Správce clusteru**: Zvolte z synchronizace Azure AD – DS správce pro váš cluster.

- **Cluster skupiny pro řetězce klíčů**: skupiny zabezpečení uživatelů chcete synchronizovat do clusteru by měl být synchronizované a k dispozici v Azure AD – DS. Například HiveUsers. Pokud chcete zadat víc skupin uživatelů, oddělte je středníkem (;). Tyto skupiny uplatněna musí existovat v adresáři před zřizování. Další informace najdete v tématu [vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Pokud skupina neexistuje, dojde k chybě: "HiveUsers skupiny nebyl nalezen ve službě Active Directory."

- **LDAPS URL**: příklad je ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Zadejte úplnou adresu URL, včetně "ldaps: / /" a číslo portu (: 636).

Následující snímek obrazovky ukazuje konfigurace na webu Azure Portal:

   ![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Další postup
* Konfigurace zásad Hivu a spouštět dotazy Hive, najdete v části [konfigurace zásad Hivu pro HDInsight clustery s ESP](apache-domain-joined-run-hive.md).
* Použití SSH pro připojení ke clusterům HDInsight s ESP, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

