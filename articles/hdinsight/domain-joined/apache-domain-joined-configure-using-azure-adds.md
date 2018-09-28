---
title: Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí služby Azure AD – DS
description: Zjistěte, jak nastavit a konfigurovat cluster HDInsight Enterprise Security Package pomocí Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408350"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí Azure Active Directory Domain Services

Clustery Enterprise Security Package (ESP) poskytují přístup k více uživatelů v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojené k doméně tak, aby uživatelé domény můžete použít své přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku se dozvíte, jak konfigurace clusteru HDInsight s ESP pomocí Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP je GA v HDI 3.6 pro Hadoop, Spark a interaktivní. ESP pro typy clusterů HBase a Kafka je ve verzi preview.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Povolení služby Azure AD – DS je předpokladem předtím, než vytvoříte HDInsight cluster s ESP. Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Pokud Azure AD – DS je povolena, všichni uživatelé a objekty zahájením z Azure Active Directory (AAD) do Azure AD – DS ve výchozím nastavení. Operace synchronizace závisí na počtu objektů v adresáři AAD. Synchronizace může trvat několik dní pro stovky tisíc objektů. 

Zákazníci si mohou vybrat synchronizaci pouze skupiny, které potřebují pracovat s clustery HDInsight. Tato možnost synchronizaci pouze určité skupiny se nazývá *obor synchronizace*. Zobrazit [konfigurace s rozsahem synchronizace ze služby Azure AD do spravované domény](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) pokyny.

> [!NOTE]
> Pouze správci tenanta máte oprávnění k vytvoření instance služby Azure AD – DS. Ověřování službou Multi-Factor Authentication je potřeba zakázat pouze pro uživatele, kteří se přístup ke clusteru.

Při povolování protokolu secure LDAP, vložte název domény do názvu subjektu nebo alternativní název subjektu v certifikátu. Například, pokud je název vaší domény *contoso.com*, ujistěte se, že přesným názvem existuje v názvu subjektu certifikátu nebo alternativní název subjektu. Další informace najdete v tématu [konfigurace zabezpečeného protokolu LDAP pro Azure AD – DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Kontrola stavu služby AAD DS

Zobrazit stav Azure Active Directory Domain Services tak, že vyberete **stavu** pod **spravovat** kategorie. Ujistěte se, že stav služby AAD DS zelený (s) a synchronizace byla dokončena.

![Stav služby Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Přidat spravované identity

Po povolení služby Azure AD – DS vytvoření uživatelsky přiřazené identity spravované a přiřaďte ho **Přispěvatel služby HDInsight domény** roli řízení přístupu Azure AD DS.

![Azure Active Directory domény služby Access control](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení spravovaných identit k **Přispěvatel služby HDInsight domény** role zajistí, že identita má přístup k provádění určitých operací služby domény v doméně AAD DS. Další informace najdete v tématu [co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s ESP

Dalším krokem je vytvoření clusteru HDInsight s ESP aktivuje pomocí služby Azure AD – DS.

Je jednodušší umístit instance služby Azure AD – DS a HDInsight cluster ve stejné virtuální síti Azure. Pokud budete chtít umístit do různých virtuálních sítí, musíte vytvořit partnerský vztah těchto virtuálních sítí, tak, aby virtuální počítače s HDInsight dohled k řadiči domény pro připojení k virtuálním počítačům. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md). Otestovat, pokud partnerský vztah provedena správně, připojení k HDInsight virtuálních sítí/podsítí virtuálního počítače a odešlete zprávu ping název domény nebo spustit **ldp.exe** do domény přístupu AAD DS.

Při vytváření clusteru služby HDInsight, máte možnost povolit Enterprise Security Package na vlastní kartě. 

![Zabezpečení služby Azure HDInsight a sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Jakmile povolíte ESP, časté nesprávné konfigurace související s Azure AD – DS bude automaticky zjistil a ověřit.

![Ověření zabezpečení domény balíčku Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Včasnou detekci šetří čas tím, že vás nutí opravovat chyby před vytvořením clusteru.

![Azure HDInsight balíčkem Enterprise security package se nezdařilo ověření domény](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Při vytváření clusteru HDInsight s ESP, je nutné zadat následující parametry:

- **Uživatel s rolí Správce clusteru**: Zvolte z synchronizace Azure AD – DS správce pro váš cluster. Tento účet musí být již synchronizované a k dispozici ve službě AAD DS.

- **Cluster skupiny pro řetězce klíčů**: skupiny zabezpečení, jejichž uživatelé chcete synchronizovat do clusteru by měly být dostupné v Azure AD-DS. Například HiveUsers skupiny. Další informace najdete v tématu [vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL**: příklad je ldaps://contoso.com:636.

Následující snímek obrazovky ukazuje úspěšné konfigurace na webu Azure Portal:

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Spravovaná identita, kterou jste vytvořili můžete zvolit ve z rozevíracího seznamu uživatelsky přiřazené identity spravované při vytváření nového clusteru.

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Další postup
* Konfigurace zásad Hivu a spouštět dotazy Hive, najdete v části [konfigurace zásad Hivu pro HDInsight clustery s ESP](apache-domain-joined-run-hive.md).
* Použití SSH pro připojení ke clusterům HDInsight s ESP, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).