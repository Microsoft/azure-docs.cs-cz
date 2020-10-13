---
title: Podnikové zabezpečení s Azure služba AD DS – Azure HDInsight
description: Naučte se, jak nastavit a nakonfigurovat cluster HDInsight Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 7792ac688ede32155ec32e1f4ba25b328102f86c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079832"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfigurace Balíček zabezpečení podniku s využitím Azure Active Directory Domain Services ve službě HDInsight

Clustery Balíček zabezpečení podniku (ESP) poskytují přístup k více uživatelům v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojené k doméně. Toto připojení umožňuje uživatelům domény používat přihlašovací údaje domény k ověřování pomocí clusterů a spouštění úloh s velkými objemy dat.

V tomto článku se dozvíte, jak nakonfigurovat cluster HDInsight pomocí protokolu ESP pomocí Azure Active Directory Domain Services (Azure služba AD DS).

> [!NOTE]  
> Protokol ESP je všeobecně dostupný v HDInsight 3,6 a 4,0 pro tyto typy clusterů: Apache Spark, Interactive, Hadoop a HBA. Protokol ESP pro Apache Kafka typ clusteru je ve verzi Preview s nejvyšší podporou jenom pro nejvyšší úsilí. Clustery ESP vytvořené před datem ESP GA (1. října 2018) nejsou podporovány.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

> [!NOTE]  
> Oprávnění k povolení služby Azure služba AD DS mají pouze správci klientů. Pokud je úložiště clusteru Azure Data Lake Storage Gen1 nebo Gen2, musíte zakázat Azure Multi-Factor Authentication jenom pro uživatele, kteří budou potřebovat přístup ke clusteru pomocí základního ověřování pomocí protokolu Kerberos.
>
> Pomocí [důvěryhodných IP adres](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) nebo [podmíněného přístupu](../../active-directory/conditional-access/overview.md) můžete Multi-Factor Authentication pro konkrétní uživatele zakázat *jenom* v případě, že přistupují k rozsahu IP adres pro virtuální síť clusteru HDInsight. Pokud používáte podmíněný přístup, ujistěte se, že koncový bod služby Active Directory je ve virtuální síti HDInsight povolený.
>
> Pokud je úložištěm objektů BLOB v Azure, nepovolujte Multi-Factor Authentication.

Povolení služby Azure služba AD DS je předpokladem, než můžete vytvořit cluster HDInsight s protokolem ESP. Další informace najdete v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

Když je povolená služba Azure služba AD DS, všechny uživatele a objekty se ve výchozím nastavení začnou synchronizovat z Azure Active Directory (Azure AD) do Azure služba AD DS. Délka operace synchronizace závisí na počtu objektů v Azure AD. Pro stovky tisíc objektů může synchronizace trvat několik dní.

Název domény, který používáte se službou Azure služba AD DS, musí mít 39 znaků nebo méně, aby bylo možné pracovat se službou HDInsight.

Můžete se rozhodnout synchronizovat jenom skupiny, které potřebují přístup ke clusterům HDInsight. Tato možnost synchronizace pouze některých skupin se nazývá *rozsah synchronizace*. Pokyny najdete v tématu [Konfigurace vymezené synchronizace z Azure AD do spravované domény](../../active-directory-domain-services/scoped-synchronization.md).

Pokud povolujete zabezpečený protokol LDAP, zadejte název domény do pole název subjektu. A alternativní název subjektu v certifikátu. Pokud je název domény *contoso100.onmicrosoft.com*, ujistěte se, že v názvu subjektu certifikátu a alternativním názvu subjektu existuje přesný název. Další informace najdete v tématu [Konfigurace protokolu Secure LDAP pro spravovanou doménu Azure služba AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Následující příklad vytvoří certifikát podepsaný svým držitelem. Název domény *contoso100.onmicrosoft.com* je v obou `Subject` (název subjektu) i v `DnsName` alternativním názvu subjektu.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Ověření stavu služby Azure služba AD DS

Zobrazení stavu Azure Active Directory Domain Services výběrem možnosti **stav** v kategorii **Spravovat** . Ujistěte se, že stav služby Azure služba AD DS je zelený (spuštěný) a synchronizace je dokončená.

![Stav služby Azure služba AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Vytvoření a autorizace spravované identity

K zjednodušení operací zabezpečeného doménových služeb použijte *uživatelsky přiřazenou spravovanou identitu* . Když přiřadíte roli **Přispěvatel služby HDInsight Domain Services** ke spravované identitě, může to číst, vytvářet, upravovat a odstraňovat operace služby Domain Services.

Pro HDInsight Balíček zabezpečení podniku jsou potřeba některé operace služby Domain Services, jako je vytváření organizačních jednotek a instančních objektů. Spravované identity můžete vytvořit v jakémkoli předplatném. Další informace o spravovaných identitách obecně najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](../hdinsight-managed-identities.md).

Pokud chcete nastavit clustery ESP, vytvořte spravovanou identitu přiřazenou uživatelem, pokud ji ještě nemáte. Viz [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .

V dalším kroku přiřaďte roli **Přispěvatel doménových služeb HDInsight** spravované identitě v **řízení přístupu** pro Azure služba AD DS. K provedení tohoto přiřazení role potřebujete oprávnění správce Azure služba AD DS.

![Řízení přístupu Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení role **Přispěvatel doménových služeb HDInsight** zajišťuje, že tato identita má správný ( `on behalf of` ) přístup k operacím služby domény v doméně Azure služba AD DS. Tyto operace zahrnují vytváření a odstraňování organizačních jednotek.

Po tom, co má spravovaná identita přiřazenou roli, správce Azure služba AD DS spravuje, kdo ji používá. Nejdřív správce vybere spravovanou identitu na portálu. Pak v části **Přehled**vybere **Access Control (IAM)** . Správce přiřadí roli **spravovaného operátora identity** uživatelům nebo skupinám, které chtějí vytvářet clustery ESP.

Správce Azure služba AD DS může například přiřadit tuto roli ke skupině **MarketingTeam** pro spravovanou identitu **sjmsi** . Příklad je znázorněn na následujícím obrázku. Toto přiřazení zajišťuje správným lidem v organizaci použití spravované identity k vytváření clusterů ESP.

![Přiřazení role operátora spravované identity HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

> [!NOTE]  
> Azure služba AD DS musí být nasazené ve virtuální síti založené na Azure Resource Manager. Klasické virtuální sítě se pro Azure služba AD DS nepodporují. Další informace najdete v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Povolte Azure služba AD DS. Místní server DNS (Domain Name System) se pak spouští na virtuálních počítačích služby Active Directory. Nakonfigurujte virtuální síť Azure služba AD DS tak, aby používala tyto vlastní servery DNS. Pokud chcete najít správné IP adresy, vyberte v kategorii **Spravovat** možnost **vlastnosti** a podívejte se **na IP adresa ve virtuální síti**.

![Vyhledání IP adres pro místní servery DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Změňte konfiguraci serverů DNS ve virtuální síti Azure služba AD DS. Pokud chcete použít tyto vlastní IP adresy, vyberte **servery DNS** v kategorii **Nastavení** . Pak vyberte možnost **vlastní** , do textového pole zadejte první IP adresu a vyberte **Uložit**. Pomocí stejných kroků přidejte další IP adresy.

![Aktualizuje se konfigurace DNS virtuální sítě.](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Instanci Azure služba AD DS i cluster HDInsight je snazší umístit do stejné virtuální sítě Azure. Pokud máte v úmyslu používat jiné virtuální sítě, je nutné, aby byly tyto virtuální sítě partnerské, aby byl řadič domény viditelný pro virtuální počítače HDInsight. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

Po vytvoření partnerského vztahu virtuálních sítí nakonfigurujte virtuální síť HDInsight tak, aby používala vlastní server DNS. A jako adresy serverů DNS zadejte privátní IP adresy Azure služba AD DS. Pokud obě virtuální sítě používají stejné servery DNS, bude se název vaší vlastní domény překládat na správnou IP adresu a bude dostupný ze služby HDInsight. Například pokud je název domény `contoso.com` , pak po tomto kroku by se měl tento krok `ping contoso.com` vyřešit na správnou služba AD DS IP adresu Azure.

![Konfigurace vlastních serverů DNS pro partnerský virtuální síť](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Pokud ve své podsíti HDInsight používáte pravidla skupiny zabezpečení sítě (NSG), měli byste povolit [požadované IP adresy](../hdinsight-management-ip-addresses.md) pro příchozí i odchozí provoz.

Pokud chcete otestovat nastavení sítě, připojte virtuální počítač s Windows k virtuální síti nebo podsíti HDInsight a otestujte název domény. (Mělo by se vyřešit na IP adresu.) Pro přístup k doméně Azure služba AD DS spusťte **ldp.exe** . Pak se připojte k tomuto virtuálnímu počítači s Windows k doméně a potvrďte, že všechna požadovaná volání RPC mezi klientem a serverem jsou úspěšná.

Pomocí nástroje **nslookup** potvrďte síťový přístup k vašemu účtu úložiště. Nebo jakoukoli externí databázi, kterou můžete použít (například externí metastore Hive nebo Ranger DB). Ujistěte se, že [požadované porty](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) jsou povolené v pravidlech NSG podsítě Azure služba AD DS, pokud NSG zabezpečuje Azure služba AD DS. Pokud je připojení k tomuto virtuálnímu počítači s Windows úspěšné, můžete pokračovat k dalšímu kroku a vytvořit clustery ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight s protokolem ESP

Po nastavení předchozích kroků v dalším kroku vytvoříte cluster HDInsight s povoleným protokolem ESP. Když vytváříte cluster HDInsight, můžete povolit Balíček zabezpečení podniku na kartě **zabezpečení + sítě** . Pro šablonu Azure Resource Manager pro nasazení použijte prostředí portálu jednou. Pak si stáhněte šablonu, která je vyplněná na stránce **Revize + vytvořit** pro pozdější opakované použití.

Během vytváření clusteru můžete taky povolit funkci [zprostředkovatel ID HDInsight](identity-broker.md) . Funkce Service Broker vám umožňuje přihlásit se k Ambari pomocí Multi-Factor Authentication a získat požadované lístky protokolu Kerberos bez nutnosti používat hodnoty hash hesel ve službě Azure služba AD DS.

> [!NOTE]  
> Prvních šest znaků názvů clusterů ESP musí být ve vašem prostředí jedinečné. Pokud máte například několik clusterů ESP v různých virtuálních sítích, vyberte zásadu vytváření názvů, která zajistí, že prvních šest znaků v názvech clusterů je jedinečných.

![Ověřování domény pro Azure HDInsight Balíček zabezpečení podniku](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Po povolení protokolu ESP se automaticky zjišťují a ověřují běžné chybné konfigurace související s Azure služba AD DS. Po opravě těchto chyb můžete pokračovat dalším krokem.

![Nepovedlo se ověřit doménu Balíček zabezpečení podniku Azure HDInsight.](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Když vytvoříte cluster HDInsight s protokolem ESP, je nutné dodat následující parametry:

* **Uživatel s rolí správce clusteru**: Vyberte správce pro váš cluster z synchronizované instance služby Azure služba AD DS. Tento doménový účet už musí být synchronizovaný a dostupný v Azure služba AD DS.

* **Skupiny přístupu clusteru**: skupiny zabezpečení, jejichž uživatelé chcete synchronizovat a mají přístup ke clusteru, by měly být k dispozici v Azure služba AD DS. Příkladem je skupina HiveUsers. Další informace najdete v tématu [Vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **Adresa URL LDAPS**: příklad je `ldaps://contoso.com:636` .

Spravovanou identitu, kterou jste vytvořili, si můžete vybrat z rozevíracího seznamu **uživatelsky přiřazené spravované identity** při vytváření nového clusteru.

![Azure HDInsight ESP Active Directory Domain Services spravovaná identita](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci zásad podregistru a spouštění dotazů na podregistr najdete v tématu [Konfigurace zásad Apache Hive pro clustery HDInsight s](apache-domain-joined-run-hive.md)protokolem ESP.
* Informace o použití SSH pro připojení ke clusterům HDInsight s protokolem ESP najdete v tématu [Použití SSH se systémem linux Apache Hadoop v HDInsight ze systému Linux, UNIX nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
