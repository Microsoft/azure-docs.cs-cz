---
title: Enterprise Security Package konfigurací pomocí Azure Active Directory Domain Services – Azure HDInsight
description: Zjistěte, jak nastavit a konfigurovat cluster HDInsight Enterprise Security Package pomocí Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/09/2018
ms.custom: seodec18
ms.openlocfilehash: 115604d9b2aa21018742bbedbc737405b52599e4
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188942"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí Azure Active Directory Domain Services

Clustery Enterprise Security Package (ESP) poskytují přístup k více uživatelů v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojené k doméně tak, aby uživatelé domény můžete použít své přihlašovací údaje domény k ověření s clustery a spouštění úloh big data. 

V tomto článku se dozvíte, jak konfigurace clusteru HDInsight s ESP pomocí Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]  
>ESP je GA v HDI 3.6 pro Apache Spark, Interactive a Apache Hadoop. ESP pro typy clusterů Apache HBase a Apache Kafka je ve verzi preview.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

> [!NOTE]  
> Pouze správci tenanta nemá oprávnění pro povolení služby Azure AD – DS. Pokud je úložiště clusteru služby Azure Data Lake Storage (ADLS) Gen1 a Gen2, zakažte Vícefaktorové ověřování (MFA) pouze pro uživatele, kteří se potřebují přístup ke clusteru. Pokud je cluster úložiště Azure Blob Storage (WASB), nezakazujte vícefaktorové ověřování.

Povolení služby Azure AD DS je předpokladem předtím, než vytvoříte HDInsight cluster s ESP. Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Pokud Azure AD – DS je povolena, všichni uživatelé a objekty zahájením z Azure Active Directory (AAD) do Azure AD – DS ve výchozím nastavení. Operace synchronizace závisí na počtu objektů ve službě Azure AD. Synchronizace může trvat několik dní pro stovky tisíc objektů. 

Zákazníci si mohou vybrat synchronizaci pouze skupiny, které potřebují pracovat s clustery HDInsight. Tato možnost synchronizaci pouze určité skupiny se nazývá *obor synchronizace*. Zobrazit [konfigurace s rozsahem synchronizace ze služby Azure AD do spravované domény](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) pokyny.

Při povolování protokolu secure LDAP, vložte název domény do názvu subjektu a alternativní název subjektu v certifikátu. Například, pokud je název vaší domény *contoso100.onmicrosoft.com*, ujistěte se, že přesným názvem existuje v názvu subjektu certifikátu a alternativní název subjektu. Další informace najdete v tématu [konfigurace zabezpečeného protokolu LDAP pro Azure AD – DS spravované domény](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Níže je příklad vytvoření certifikátu podepsaného svým držitelem a jste název domény (*contoso100.onmicrosoft.com*) v názvu subjektu a DnsName (alternativní název subjektu):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
``` 

## <a name="check-azure-ad-ds-health-status"></a>Kontrola stavu služby Azure AD – DS
Zobrazit stav Azure Active Directory Domain Services tak, že vyberete **stavu** pod **spravovat** kategorie. Ujistěte se, že je stav Azure AD – DS zelený (s) a synchronizace byla dokončena.

![Stav služby Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Vytvořit a autorizovat spravované identity

A **uživatelsky přiřazené identity spravované** slouží ke zjednodušení a bezpečný provoz služby domény. Když přiřadíte roli přispěvatele služby HDInsight domény pro spravovanou identitu, ho čtení, vytvořit, upravit a Odstranit operacích v doméně služby. Některé operace, jako je vytvoření organizační jednotky služby domain services a zásady služby jsou nezbytné k HDInsight Enterprise Security Package. Spravované identity je možné vytvořit v libovolné předplatné. Další informace najdete v tématu [spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pokud chcete nastavit ESP clustery, vytvoření uživatelsky přiřazené spravovanou identitu Pokud již nemáte. Zobrazit [Create, seznam, delete nebo přiřadit roli, kterou chcete přiřadit uživatele spravovanou identitu pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) pokyny. Dále přiřaďte **Přispěvatel služby HDInsight domény** role spravované identity v Azure AD DS Access control (oprávnění správce AAD DS jsou nezbytné pro toto přiřazení role).

![Azure Active Directory domény služby Access control](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení **Přispěvatel služby HDInsight domény** role zajistí, že tato identita má správný přístup k provádění operací služby domény jako je například vytváření organizační jednotky, odstraňování organizační jednotky, atd. v doméně AAD DS (jménem).

Jakmile spravovaná identita je vytvořen a daný správnou roli, můžete nastavit správce AAD DS používající tuto spravovanou identitu. Nastavení uživatelů pro spravovanou identitu, musí správce vybrat spravovanou identitu na portálu a potom klikněte na **řízení přístupu (IAM)** pod **přehled**. Pak na pravé straně, přiřaďte **operátor spravovaných identit** role uživatelům nebo skupinám, které chcete k vytvoření clusterů HDInsight ESP. Například můžete přiřadit správce AAD DS tuto roli do skupiny "MarketingTeam" identity "sjmsi" spravované, jak je znázorněno na obrázku níže. Tím se zajistí, že lidé v organizaci mají přístup k této spravované identity za účelem vytváření clusterů ESP.

![HDInsight Spravovat přiřazení rolí Identity – operátor](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Aspekty sítí

> [!NOTE]  
> Azure AD DS musí být nasazený ve virtuální síti na základě Azure Resource Manageru (ARM). Klasické virtuální sítě nejsou podporovány pro Azure AD – DS. Najdete [povolit Azure Active Directory Domain Services pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-network) další podrobnosti.

Po povolení služby Azure AD-DS místní server služby DNS (Domain Name) běží na virtuálních počítačích (VM) AD. Konfigurace služby AD DS virtuální sítě (virtuální sítě Azure) používat tyto vlastní servery DNS. Chcete-li najít správné IP adresy, vyberte **vlastnosti** pod **spravovat** kategorie a podívejte se na IP adresy uvedené pod **IP adresu ve virtuální síti**.

![Vyhledejte IP adres pro místní servery](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Změna konfigurace serverů DNS ve virtuální síti Azure AD DS použití těchto vlastních IP adres tak, že vyberete **servery DNS** pod **nastavení** kategorie. Klikněte na přepínač vedle **vlastní**, zadejte první IP adresu do příslušného textového pole a klikněte na tlačítko **Uložit**. Přidejte další IP adresy pomocí stejného postupu.

![Aktualizuje se konfigurace DNS virtuální sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Je jednodušší umístit instance služby Azure AD – DS a HDInsight cluster ve stejné virtuální síti Azure. Pokud budete chtít použít jiné virtuální sítě, musíte vytvořit partnerský vztah těchto virtuálních sítí, tak, aby řadič domény je viditelná pro virtuální počítače HDI. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md). 

Po vytvoření partnerského vztahu virtuálních sítí, konfigurovat virtuální síť HDInsight použít vlastní server DNS a zadejte privátní IP adresy služby Azure AD – DS jako adresy serverů DNS. Když obou virtuálních sítích používat stejné servery DNS, vlastní název domény se přeloží správné IP a bude dostupný v HDInsight. Například pokud je název domény "contoso.com" pak po provedení tohoto kroku příkaz ping "contoso.com" musí se překládat na pravé straně IP adres Azure AD DS. 

![Konfigurace vlastního DNS serverů pro partnerské virtuální síti](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Pokud používáte pravidla skupiny zabezpečení sítě (NSG) v podsíti služby HDInsight, měli byste si nechat [požadované IP adresy](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#hdinsight-ip-1) pro příchozí i odchozí provoz. 

**K otestování** Pokud sítě je správně nastavené, připojení k HDInsight virtuálních sítí/podsítí virtuálního počítače s windows a pomocí příkazu ping název domény (ho musí se překládat na IP adresy) a potom spusťte **ldp.exe** pro přístup k doméně Azure AD – DS. Potom **připojení tohoto virtuálního počítače s windows do domény potvrďte** úspěšný všechny požadované volání RPC mezi klientem a serverem. Můžete také použít **nslookup** potvrďte připojení k účtu úložiště nebo všechny externí databáze, můžete použít (například externí Hive metastore nebo Ranger DB).
Ujistěte se, že všechny [požadované porty](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) jsou povolené v podsíti služby AAD DS pravidla skupiny zabezpečení sítě, pokud AAD DS je zabezpečena pomocí skupiny zabezpečení sítě. Pokud připojení k doméně systému windows tohoto virtuálního počítače je úspěšné, můžete pokračovat k dalšímu kroku a vytvoření ESP clusterů.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s ESP

Po nastavení v předchozích krocích správně, dalším krokem je vytvoření clusteru HDInsight s ESP povolena. Při vytváření clusteru služby HDInsight můžete povolit balíčkem Enterprise Security Package **vlastní** kartu. Pokud chcete použít šablonu Azure Resource Manageru pro nasazení, použijte portál jednou a stáhněte předem vyplněných šablon pro budoucí využití bude poslední stránka "Přehled".

![Ověření zabezpečení domény balíčku Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Jakmile povolíte ESP, časté nesprávné konfigurace související s Azure AD – DS bude automaticky zjistil a ověřit. Po opravě těchto chyb můžete pokračovat na další krok: 

![Azure HDInsight balíčkem Enterprise security package se nezdařilo ověření domény](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Při vytváření clusteru HDInsight s ESP, je nutné zadat následující parametry:

- **Uživatel s rolí Správce clusteru**: Vyberte správce pro váš cluster z synchronizace Azure AD – DS. Tento účet domény musí být již synchronizované a k dispozici v Azure AD – DS.

- **Cluster skupiny pro řetězce klíčů**: Skupiny zabezpečení uživatelů chcete synchronizovat a mít přístup ke clusteru by měl být k dispozici v Azure AD – DS. Například HiveUsers skupiny. Další informace najdete v tématu [vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL**: Příkladem je ldaps://contoso.com:636.

Následující snímek obrazovky ukazuje úspěšná konfigurace na webu Azure Portal:

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Spravovaná identita, kterou jste vytvořili můžete zvolit ve z rozevíracího seznamu uživatelsky přiřazené identity spravované při vytváření nového clusteru.

![Konfigurace Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Další postup
* Konfigurace zásad Hivu a spouštět dotazy Hive, najdete v části [zásad konfigurace Apache Hivu pro HDInsight clustery s ESP](apache-domain-joined-run-hive.md).
* Použití SSH pro připojení ke clusterům HDInsight s ESP, naleznete v tématu [použití SSH se systémem Linux Apache Hadoop v HDInsight ze systému Linux, Unix nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
