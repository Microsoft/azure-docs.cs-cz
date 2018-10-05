---
title: Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí služby Azure AD – DS
description: Zjistěte, jak nastavit a konfigurovat cluster HDInsight Enterprise Security Package pomocí Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/3/2018
ms.openlocfilehash: 84ee24b9002237d0993a30190944dbd6dd190ac8
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784931"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí Azure Active Directory Domain Services

Clustery Enterprise Security Package (ESP) poskytují přístup k více uživatelů v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojené k doméně tak, aby uživatelé domény můžete použít své přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku se dozvíte, jak konfigurace clusteru HDInsight s ESP pomocí Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP je GA v HDI 3.6 pro Hadoop, Spark a interaktivní. ESP pro typy clusterů HBase a Kafka je ve verzi preview.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Povolení služby Azure AD DS je předpokladem předtím, než vytvoříte HDInsight cluster s ESP. Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Pokud Azure AD – DS je povoleno, všechny uživatele a objekty start, synchronizaci ze služby Azure Active Directory k Azure AD – DS ve výchozím nastavení. Operace synchronizace závisí na počtu objektů ve službě Azure AD. Synchronizace může trvat několik dní pro stovky tisíc objektů. 

Zákazníci si mohou vybrat synchronizaci pouze skupiny, které potřebují pracovat s clustery HDInsight. Tato možnost synchronizaci pouze určité skupiny se nazývá *obor synchronizace*. Zobrazit [konfigurace s rozsahem synchronizace ze služby Azure AD do spravované domény](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) pokyny.

Po povolení služby Azure AD-DS místní server služby DNS (Domain Name) běží na virtuálních počítačích (VM) AD. Konfigurace služby AD DS virtuální sítě (virtuální sítě Azure) používat tyto vlastní servery DNS. Chcete-li najít správné IP adresy, vyberte **vlastnosti** pod **spravovat** kategorie a podívejte se na IP adresy uvedené pod **IP adresu ve virtuální síti**.

![Vyhledejte IP adres pro místní servery](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Změna konfigurace serverů DNS ve virtuální síti Azure AD DS použití těchto vlastních IP adres tak, že vyberete **servery DNS** pod **nastavení** kategorie. Klikněte na přepínač vedle **vlastní**, zadejte první IP adresu do příslušného textového pole a klikněte na tlačítko **Uložit**. Přidejte další IP adresy pomocí stejného postupu.

![Aktualizuje se konfigurace DNS virtuální sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

> [!NOTE]
> Pouze správci tenanta máte oprávnění k vytvoření instance služby Azure AD – DS. Ověřování službou Multi-Factor Authentication je potřeba zakázat pouze pro uživatele, kteří se přístup ke clusteru.

Při povolování protokolu secure LDAP, vložte název domény do názvu subjektu nebo alternativní název subjektu v certifikátu. Například, pokud je název vaší domény *contoso.com*, ujistěte se, že přesným názvem existuje v názvu subjektu certifikátu nebo alternativní název subjektu. Další informace najdete v tématu [konfigurace zabezpečeného protokolu LDAP pro Azure AD – DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-azure-ad-ds-health-status"></a>Kontrola stavu služby Azure AD – DS

Zobrazit stav Azure Active Directory Domain Services tak, že vyberete **stavu** pod **spravovat** kategorie. Ujistěte se, že je stav Azure AD – DS zelený (s) a synchronizace byla dokončena.

![Stav služby Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Přidat spravované identity

Vytvoření uživatelsky přiřazené spravovanou identitu, pokud již nemáte. Zobrazit [Create, seznam, delete nebo přiřadit roli, kterou chcete přiřadit uživatele spravovanou identitu pomocí webu Azure portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) pokyny. 

Spravovaná identita se používá k zjednodušení operací služby domény. Tato identita má přístup, číst, vytvářet, upravovat a odstraňovat doménové služby operace, které jsou potřebné pro HDInsight Enterprise Security Package, jako je vytvoření organizační jednotky a principů služby.

Po povolení služby Azure AD – DS vytvoření uživatelsky přiřazené identity spravované a přiřaďte ho **Přispěvatel služby HDInsight domény** roli řízení přístupu Azure AD DS.

![Azure Active Directory domény služby Access control](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení spravovaných identit k **Přispěvatel služby HDInsight domény** role zajistí, že identita má přístup k provádění určitých operací služby domény v doméně služby Azure AD – DS. Další informace najdete v tématu [co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s ESP

Dalším krokem je vytvoření clusteru HDInsight s ESP aktivuje pomocí služby Azure AD – DS.

Je jednodušší umístit instance služby Azure AD – DS a HDInsight cluster ve stejné virtuální síti Azure. Pokud jsou v různých virtuálních sítích, musíte vytvořit partnerský vztah těchto virtuálních sítí, tak, aby virtuální počítače s HDInsight jsou viditelné na řadič domény a mohou být přidány do domény. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md). Otestovat, pokud partnerský vztah provedena správně, připojení k HDInsight virtuálních sítí/podsítí virtuálního počítače a odešlete zprávu ping název domény nebo spustit **ldp.exe** pro přístup k doméně Azure AD – DS.

Po vytvoření partnerského vztahu virtuálních sítí, konfigurovat virtuální síť HDInsight použít vlastní server DNS a zadejte privátní IP adresy služby Azure AD – DS jako adresy serverů DNS. Když obou virtuálních sítích používat stejné servery DNS, vlastní název domény se přeloží správné IP a bude dostupný v HDInsight. Například pokud je název domény "contoso.com" pak po provedení tohoto kroku příkaz ping "contoso.com" musí se překládat na pravé straně IP adres Azure AD DS. Virtuální počítač pak můžete připojit k této doméně.

![Konfigurace vlastního DNS serverů pro partnerské virtuální síti](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Při vytváření clusteru služby HDInsight můžete povolit Enterprise Security Package na vlastní kartě.

![Zabezpečení služby Azure HDInsight a sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Jakmile povolíte ESP, časté nesprávné konfigurace související s Azure AD – DS bude automaticky zjistil a ověřit.

![Ověření zabezpečení domény balíčku Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Včasnou detekci šetří čas tím, že vás nutí opravovat chyby před vytvořením clusteru.

![Azure HDInsight balíčkem Enterprise security package se nezdařilo ověření domény](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Při vytváření clusteru HDInsight s ESP, je nutné zadat následující parametry:

- **Uživatel s rolí Správce clusteru**: Zvolte z synchronizace Azure AD – DS správce pro váš cluster. Tento účet musí být již synchronizované a k dispozici v Azure AD – DS.

- **Cluster skupiny pro řetězce klíčů**: skupiny zabezpečení, jejichž uživatelé chcete synchronizovat do clusteru by měly být dostupné v Azure AD-DS. Například HiveUsers skupiny. Další informace najdete v tématu [vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL**: příklad je ldaps://contoso.com:636.

Následující snímek obrazovky ukazuje úspěšná konfigurace na webu Azure Portal:

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Spravovaná identita, kterou jste vytvořili můžete zvolit ve z rozevíracího seznamu uživatelsky přiřazené identity spravované při vytváření nového clusteru.

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Další postup
* Konfigurace zásad Hivu a spouštět dotazy Hive, najdete v části [konfigurace zásad Hivu pro HDInsight clustery s ESP](apache-domain-joined-run-hive.md).
* Použití SSH pro připojení ke clusterům HDInsight s ESP, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
