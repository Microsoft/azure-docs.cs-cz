---
title: Balíček zabezpečení podniku s Azure Active Directory ve službě HDInsight
description: Naučte se, jak nastavit a nakonfigurovat cluster HDInsight Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: 76f95e74c8150ac797d20c3166c0e8d6ea085bf9
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734951"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurace clusteru HDInsight s Balíčkem zabezpečení podniku pomocí služby Azure Active Directory Domain Services

Clustery Balíček zabezpečení podniku (ESP) poskytují přístup k více uživatelům v clusterech Azure HDInsight. Clustery HDInsight s protokolem ESP jsou připojené k doméně, aby uživatelé domény mohli k ověřování s clustery používat svoje přihlašovací údaje v doméně a spouštět úlohy s velkými objemy dat.

V tomto článku se dozvíte, jak pomocí Azure Active Directory Domain Services (Azure AD-DS) nakonfigurovat cluster HDInsight s protokolem ESP.

> [!NOTE]  
> Protokol ESP je všeobecně dostupný v HDInsight 3,6 a 4,0 pro typy clusterů: Apache Spark, Interactive, Hadoop a HBA. Protokol ESP pro Apache Kafka typ clusteru je ve verzi Preview s podporou pouze nejlepšího úsilí. Clustery ESP vytvořené před datem GA protokolu ESP (1. října 2018) nejsou podporovány.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD-DS

> [!NOTE]  
> Oprávnění k povolení služby Azure AD-DS mají jenom správci tenanta. Pokud je úložiště clusteru Azure Data Lake Storage (ADLS) Gen1 nebo Gen2, je nutné zakázat Multi-Factor Authentication (MFA) pouze pro uživatele, kteří budou potřebovat přístup ke clusteru pomocí základních ověření protokolu Kerberos. Pomocí [důvěryhodných IP adres](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) nebo [podmíněného přístupu](../../active-directory/conditional-access/overview.md) můžete zakázat MFA pro konkrétní uživatele, jenom když přistupuje k rozsahu IP adres virtuální sítě clusteru HDInsight. Pokud používáte podmíněný přístup, ujistěte se, že koncový bod služby AD ve virtuální síti HDInsight je povolený.
>
> Pokud je úložiště clusteru Azure Blob Storage (WASB), nepovolujte vícefaktorové ověřování.

Povolení služby AzureAD-DS je předpokladem, než můžete vytvořit cluster HDInsight s protokolem ESP. Další informace najdete v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md). 

Když je povolená služba Azure AD-DS, všichni uživatelé a objekty se ve výchozím nastavení začnou synchronizovat z Azure Active Directory (AAD) do Azure AD-DS. Délka operace synchronizace závisí na počtu objektů v Azure AD. Synchronizace může pro stovky tisíc objektů trvat několik dní. 

Název domény, který používáte se službou Azure AD-DS, musí mít 39 znaků nebo méně, aby bylo možné pracovat se službou HDInsight.

Můžete se rozhodnout synchronizovat jenom skupiny, které potřebují přístup ke clusterům HDInsight. Tato možnost synchronizace pouze některých skupin se nazývá *rozsah synchronizace*. Pokyny najdete v tématu [Konfigurace synchronizace s vymezeným oborem z Azure AD do spravované domény](../../active-directory-domain-services/scoped-synchronization.md) .

Pokud povolíte zabezpečený protokol LDAP, zadejte název domény do pole název subjektu a alternativní název subjektu v certifikátu. Pokud je název domény například *contoso100.onmicrosoft.com*, ujistěte se, že v názvu subjektu certifikátu a alternativním názvu předmětu existuje přesný název. Další informace najdete v tématu [Konfigurace protokolu Secure LDAP pro spravovanou doménu Azure AD – DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). Níže je uveden příklad vytvoření certifikátu podepsaného svým držitelem a název domény (*contoso100.onmicrosoft.com*) v názvu subjektu i v DnsName (alternativní název subjektu):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Ověření stavu služby Azure AD – DS
Zobrazte stav svého Azure Active Directory Domain Services výběrem možnosti **stav** v kategorii **Spravovat** . Ujistěte se, že stav služby Azure AD-DS je zelený (spuštěný) a synchronizace je dokončená.

![Azure Active Directory Domain Services stav](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Vytvoření a autorizace spravované identity

**Spravovaná identita přiřazená uživatelem** slouží ke zjednodušení a zabezpečení operací služby Domain Services. Když přiřadíte roli Přispěvatel služby HDInsight Domain Services ke spravované identitě, může to číst, vytvářet, upravovat a odstraňovat operace služby Domain Services. Pro Balíček zabezpečení podniku HDInsight jsou potřeba některé operace služby Domain Services, jako je vytváření organizačních jednotek a instančních objektů. Spravované identity je možné vytvořit v jakémkoli předplatném. Další informace o spravovaných identitách obecně najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](../hdinsight-managed-identities.md).

Pokud chcete nastavit clustery ESP, vytvořte spravovanou identitu přiřazenou uživatelem, pokud ji ještě nemáte. Pokyny najdete v tématu [Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . V dalším kroku přiřaďte roli **Přispěvatel doménových služeb HDInsight** spravované identitě ve službě Azure AD-DS řízení přístupu (k provedení tohoto přiřazení role se vyžadují oprávnění správce AAD-DS).

![Řízení přístupu Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení role **Přispěvatel doménových služeb HDInsight** zajišťuje, že tato identita má správný přístup k operacím doménových služeb, jako je vytváření organizačních jednotek, odstraňování organizačních jednotek atd., a to v doméně AAD-DS.

Po vytvoření spravované identity a správné role může správce AAD-DS nastavit, kdo může tuto spravovanou identitu používat. Pro nastavení uživatelů pro spravovanou identitu by měl správce na portálu vybrat spravovanou identitu a potom v části **Přehled**kliknout na **Access Control (IAM)** . Pak na pravé straně přiřaďte roli **spravovaného operátora identity** uživatelům nebo skupinám, které chtějí vytvářet clustery HDInsight ESP. Správce AAD-DS může například přiřadit tuto roli ke skupině **MarketingTeam** pro spravovanou identitu **sjmsi** , jak je znázorněno na následujícím obrázku. Tím se zajistí, aby měli oprávnění k použití této spravované identity pro účely vytváření clusterů ESP přístup správné osoby v organizaci.

![Přiřazení role operátora spravované identity HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Aspekty sítí

> [!NOTE]  
> Služba Azure AD-DS musí být nasazená ve virtuální síti založené na Azure Resource Manager. Klasické virtuální sítě se pro Azure AD-DS nepodporují. Další podrobnosti najdete [v tématu povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network) .

Po povolení služby Azure AD-DS se na Virtual Machines AD (VM) spustí místní server DNS (Domain Name Service). Nakonfigurujte službu Azure AD-DS Virtual Network (VNET), aby používala tyto vlastní servery DNS. Pokud chcete najít správné IP adresy, vyberte v kategorii **Spravovat** možnost **vlastnosti** a podívejte se na IP adresy uvedené pod položkou **IP adresa v Virtual Network**.

![Vyhledání IP adres pro místní servery DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Změňte konfiguraci serverů DNS ve virtuální síti Azure AD DS tak, aby se tyto vlastní IP adresy používaly, a to tak, že v kategorii **Nastavení** vyberete **servery DNS** . Pak klikněte na přepínač vedle **vlastní**, do textového pole níže zadejte první IP adresu a klikněte na **Uložit**. Pomocí stejných kroků přidejte další IP adresy.

![Aktualizuje se konfigurace DNS virtuální sítě.](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Je snazší umístit instanci Azure AD-DS i cluster HDInsight do stejné virtuální sítě Azure. Pokud plánujete použít jiný virtuální sítě, musíte tyto virtuální sítě navázat, aby byl řadič domény viditelný pro virtuální počítače s HDI. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md). 

Po navázání partnerského vztahu virtuální sítě nakonfigurujte virtuální síť HDInsight, aby používala vlastní server DNS, a jako adresy serverů DNS zadejte privátní IP adresy Azure AD-DS. Pokud oba virtuální sítě používají stejné servery DNS, bude se název vaší vlastní domény překládat na správnou IP adresu a bude dostupný z HDInsight. Například pokud je `contoso.com` název domény potom po tomto kroku, `ping contoso.com` měli byste ho vyřešit na správnou IP adresu služby Azure AD-DS.

![Konfigurace vlastních serverů DNS pro virtuální síť s partnerským vztahem](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Pokud ve své podsíti HDInsight používáte pravidla skupin zabezpečení sítě (NSG), měli byste povolit [požadované IP adresy](../hdinsight-management-ip-addresses.md) pro příchozí i odchozí provoz. 

Pokud **chcete otestovat** , jestli je vaše síť správně nastavená, připojte virtuální počítač s Windows k virtuální síti nebo podsíti HDInsight a otestujte název domény (musí se překládat na IP adresu) a pak pro přístup k doméně Azure AD-DS spusťte nástroj **Ldp. exe** . Pak se **připojte k tomuto virtuálnímu počítači s Windows k doméně a potvrďte** , že všechna požadovaná volání RPC mezi klientem a serverem jsou úspěšná. Pomocí nástroje **nslookup** můžete také potvrdit přístup k síti k vašemu účtu úložiště nebo libovolné externí databázi, kterou byste mohli použít (například externí metastore Hive nebo Ranger DB).
V případě, že je služba AAD-DS zabezpečená NSG, měli byste se ujistit, že všechny [požadované porty](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) jsou na seznamu povolených v rámci pravidel skupiny zabezpečení sítě AAD-DS. Pokud je připojení k tomuto virtuálnímu počítači s Windows úspěšné, můžete přejít k dalšímu kroku a vytvořit clustery ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s protokolem ESP

Po správném nastavení předchozích kroků je dalším krokem vytvoření clusteru HDInsight s povoleným protokolem ESP. Když vytváříte cluster HDInsight, můžete povolit Balíček zabezpečení podniku na **vlastní** kartě. Pokud dáváte přednost použití šablony Azure Resource Manager pro nasazení, použijte prostředí portálu jednou a stáhněte předem vyplněnou šablonu na poslední stránce Souhrn pro budoucí použití.

> [!NOTE]  
> Prvních šest znaků názvů clusterů ESP musí být ve vašem prostředí jedinečné. Pokud máte například několik clusterů ESP v různých virtuální sítě, měli byste zvolit pojmenování convension, které v názvech clusterů zajišťuje, aby prvních šest znaků bylo jedinečné.

![Ověření domény balíčku zabezpečení Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Po povolení protokolu ESP budou běžné nepropojené konfigurace týkající se služby Azure AD-DS automaticky zjišťovány a ověřovány. Po opravě těchto chyb můžete pokračovat dalším krokem: 

![Neúspěšné ověření domény balíčku zabezpečení Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Když vytvoříte cluster HDInsight s protokolem ESP, je nutné dodat následující parametry:

- **Uživatel s rolí správce clusteru**: Z synchronizované služby Azure AD DS vyberte Správce pro váš cluster. Tento účet domény musí být už synchronizovaný a dostupný ve službě Azure AD-DS.

- **Skupiny přístupu clusteru**: Skupiny zabezpečení, jejichž uživatelé chcete synchronizovat a mají přístup ke clusteru, by měly být k dispozici ve službě Azure AD-DS. Například skupina HiveUsers. Další informace najdete v tématu [Vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **ADRESA URL LDAPS**: Příklad: `ldaps://contoso.com:636`.

Následující snímek obrazovky ukazuje úspěšnou konfiguraci v Azure Portal:

![Konfigurace služby Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Spravovanou identitu, kterou jste vytvořili, si můžete vybrat v rozevíracím seznamu uživatelsky přiřazené spravované identity při vytváření nového clusteru.

![Konfigurace služby Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci zásad podregistru a spouštění dotazů na podregistr najdete v tématu [Konfigurace zásad Apache Hive pro clustery HDInsight s](apache-domain-joined-run-hive.md)protokolem ESP.
* Informace o použití SSH pro připojení ke clusterům HDInsight s protokolem ESP najdete v tématu [Použití SSH se systémem linux Apache Hadoop v HDInsight ze systému Linux, UNIX nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
