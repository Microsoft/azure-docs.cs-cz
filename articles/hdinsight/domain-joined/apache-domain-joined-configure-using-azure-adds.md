---
title: Podnikové zabezpečení s Azure AD DS – Azure HDInsight
description: Zjistěte, jak nastavit a nakonfigurovat cluster balíčků HDInsight Enterprise Security Package pomocí služby Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272835"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfigurace balíčků podnikového zabezpečení se službou Azure Active Directory Domain Services v HDInsightu

Clustery balíčků podnikového zabezpečení (ESP) poskytují víceuživatelský přístup v clusterech Azure HDInsight. Clustery HDInsight s ESP jsou připojeny k doméně, takže uživatelé domény mohou používat svá pověření domény k ověřování pomocí clusterů a spouštění úloh velkých objemů dat.

V tomto článku se dozvíte, jak nakonfigurovat cluster HDInsight s ESP pomocí služby Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP je obecně k dispozici v HDInsight 3.6 a 4.0 pro tyto typy clusterů: Apache Spark, Interaktivní, Hadoop a HBase. ESP pro typ clusteru Apache Kafka je ve verzi Preview pouze s podporou s maximálním úsilím. Clustery ESP vytvořené před datem ESP GA (1. října 2018) nejsou podporovány.

## <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

> [!NOTE]  
> Pouze správci tenanta mají oprávnění k povolení Azure AD DS. Pokud je úložiště clusteru Azure Data Lake Storage Gen1 nebo Gen2, musíte zakázat Azure Multi-Factor Authentication jenom pro uživatele, kteří budou potřebovat přístup ke clusteru pomocí základního ověřování kerberos. 
>
> [Důvěryhodné IP adresy](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) nebo podmíněný [přístup](../../active-directory/conditional-access/overview.md) můžete zakázat vícefaktorové ověřování pro konkrétní uživatele *pouze* v případě, že přistupují k rozsahu IP adres pro virtuální síť clusteru HDInsight. Pokud používáte podmíněný přístup, ujistěte se, že koncový bod služby Active Directory je povolen ve virtuální síti HDInsight.
>
> Pokud je úložiště clusteru azure blob úložiště, nezakazujte vícefaktorové ověřování.

Povolení Azure AD DS je předpokladem před vytvořením clusteru HDInsight s ESP. Další informace najdete [v tématu Povolení služby Azure Active Directory Domain Services pomocí portálu Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Když je Azure AD DS povolená, všichni uživatelé a objekty začnou ve výchozím nastavení synchronizovat z Azure Active Directory (Azure AD) na Azure AD DS. Délka operace synchronizace závisí na počtu objektů ve službě Azure AD. Synchronizace může trvat několik dní pro stovky tisíc objektů. 

Název domény, který používáte s Azure AD DS musí být 39 znaků nebo méně, pro práci s HDInsight.

Můžete synchronizovat pouze skupiny, které potřebují přístup ke clusterům HDInsight. Tato možnost synchronizace pouze určitých skupin se nazývá *synchronizace s vymezenou souplnícím rozsahem*. Pokyny najdete [v tématu Konfigurace synchronizace s vymezeným oborem z Azure AD do spravované domény](../../active-directory-domain-services/scoped-synchronization.md).

Když povolujete zabezpečené protokol LDAP, vložte název domény do názvu subjektu a alternativní název subjektu do certifikátu. Pokud je například název domény *contoso100.onmicrosoft.com*, ujistěte se, že v názvu subjektu certifikátu a alternativním názvu subjektu certifikátu existuje přesný název. Další informace najdete [v tématu Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

Následující příklad vytvoří certifikát podepsaný svým držitelem. Název domény *contoso100.onmicrosoft.com* je `Subject` v obou `DnsName` (název subjektu) a (alternativní název předmětu).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Kontrola stavu stavu služby Azure AD DS
Stav služby Azure Active Directory Domain Services zobrazíte výběrem **možnosti Stav** v kategorii **Spravovat.** Ujistěte se, že stav Azure AD DS je zelená (spuštěná) a synchronizace je dokončena.

![Azure AD DS zdraví](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Vytvoření a autorizace spravované identity

Pomocí spravované *identity přiřazené uživateli* můžete zjednodušit a pomoci zabezpečit operace doménových služeb. Když přiřadíte roli **přispěvatele služby HDInsight Domain Services** spravované identitě, může číst, vytvářet, upravovat a odstraňovat operace doménových služeb. 

Některé operace doménových služeb, jako je například vytváření provozních zdrojů a instančních objektů, jsou potřebné pro balíček HDInsight Enterprise Security Package. Spravované identity můžete vytvořit v libovolném předplatném. Další informace o spravovaných identitách obecně najdete v [tématu Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Další informace o tom, jak spravované identity fungují ve Službě Azure HDInsight, najdete [v tématu Spravované identity v Azure HDInsight](../hdinsight-managed-identities.md).

Chcete-li nastavit clustery ESP, vytvořte spravovanou identitu přiřazenou uživateli, pokud ji ještě nemáte. Pokyny najdete v [tématu Vytvoření, seznam, odstranění nebo přiřazení role spravované identitě přiřazené uživateli pomocí webu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Dále přiřaďte roli **přispěvatele doménových služeb HDInsight** spravované identitě v **řízení accessu** pro Azure AD DS. K vytvoření tohoto přiřazení role potřebujete oprávnění správce služby Azure AD DS.

![Řízení přístupu ke službě Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Přiřazení role **přispěvatele doménových služeb HDInsight** zajišťuje, že tato identita má správný (jménem) přístup k provádění operací doménových služeb v doméně Azure AD DS. Tyto operace zahrnují vytváření a odstraňování operačních operací.

Po vytvoření spravované identity a dané správné roli, správce Azure AD DS můžete nastavit, kdo můžete použít tuto spravovanou identitu. Nejprve správce vybere spravovanou identitu na portálu a potom vybere **řízení přístupu (IAM)** v části **Přehled**. Potom na pravé straně správce přiřadí roli **operátoru spravované identity** uživatelům nebo skupinám, které chtějí vytvořit clustery HDInsight ESP. 

Například správce Azure AD DS můžete přiřadit tuto roli **skupině MarketingTeam** pro spravovanou identitu **sjmsi,** jak je znázorněno na následujícím obrázku. Toto přiřazení zajišťuje, že správné osoby v organizaci mohou použít spravovanou identitu k vytvoření clusterů ESP.

![Přiřazení role operátora spravované identity HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

> [!NOTE]  
> Azure AD DS musí být nasazený ve virtuální síti založené na Azure Resource Manageru. Klasické virtuální sítě nejsou podporovány pro Azure AD DS. Další informace najdete [v tématu Povolení služby Azure Active Directory Domain Services pomocí portálu Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Po povolení Služby Azure AD DS se na virtuálních počítačích služby Active Directory spustí místní server DNS (Domain Name System). Nakonfigurujte virtuální síť Azure AD DS tak, aby používala tyto vlastní servery DNS. Chcete-li vyhledat správné adresy IP, vyberte v kategorii **Spravovat** **položku Vlastnosti** a vyhledejte v části **Adresa IP ve virtuální síti**.

![Vyhledání adres IP pro místní servery DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Změňte konfiguraci serverů DNS ve virtuální síti Azure AD DS tak, aby používaly tyto vlastní IP adresy, výběrem **serverů DNS** v kategorii **Nastavení.** Pak vyberte **možnost Vlastní,** zadejte první ADRESU IP do textového pole a vyberte **Uložit**. Přidejte další adresy IP pomocí stejných kroků.

![Aktualizace konfigurace DNS virtuální sítě](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Je jednodušší umístit instanci Azure AD DS i cluster HDInsight do stejné virtuální sítě Azure. Pokud máte v plánu používat různé virtuální sítě, musíte tyto virtuální sítě sledovat tak, aby byl řadič domény viditelný pro virtuální počítače HDInsight. Další informace naleznete v [tématu Partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md). 

Po partnerské síti virtuální sítě nakonfigurujte virtuální síť HDInsight tak, aby používala vlastní server DNS, a zadejte privátní IP adresy Služby Azure AD DS jako adresy serverů DNS. Pokud obě virtuální sítě používají stejné servery DNS, váš vlastní název domény se převrátí na správnou IP adresu a bude dostupný z HDInsightu. Například pokud název domény `contoso.com`je , pak `ping contoso.com` po tomto kroku by měl přeložit na pravé Azure AD DS IP.

![Konfigurace vlastních serverů DNS pro partnerovou virtuální síť](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Pokud používáte pravidla skupiny zabezpečení sítě (NSG) v podsíti HDInsight, měli byste povolit [požadované IP adresy](../hdinsight-management-ip-addresses.md) pro příchozí i odchozí provoz.

Chcete-li otestovat, zda je síť správně nastavená, připojte virtuální počítač se systémem Windows k virtuální síti/podsíti HDInsight a příkazem ping na název domény. (To by mělo vyřešit ip.) Spusťte **ldp.exe** pro přístup k doméně Azure AD DS. Potom připojte tento virtuální počítač se systémem Windows k doméně a potvrďte, že všechna požadovaná volání vzdáleného volání mezi klientem a serverem jsou úspěšná. 

**Nslookup** můžete také použít k potvrzení přístupu k síti k účtu úložiště nebo libovolné externí databázi, kterou můžete použít (například externí metastore Hive nebo Ranger DB). Ujistěte se, že všechny [požadované porty](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) jsou povoleny v pravidlech sítě zabezpečení sítě Azure AD DS, pokud skupina zabezpečení sítě pomáhá zabezpečit Azure AD DS. Pokud je připojení domény tohoto virtuálního aplikace systému Windows úspěšné, můžete pokračovat k dalšímu kroku a vytvořit clustery ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Vytvoření clusteru HDInsight pomocí ESP

Po správném nastavení předchozích kroků je dalším krokem vytvoření clusteru HDInsight s povoleným protokolem ESP. Při vytváření clusteru HDInsight můžete povolit balíček enterprise security na kartě **Zabezpečení + sítě.** Pokud dáváte přednost použití šablony Azure Resource Manager pro nasazení, použijte prostředí portálu jednou a stáhněte si předvyplněnou šablonu na stránce **Revize + vytvoření** pro budoucí opakované použití. 

Můžete také povolit funkci [HDInsight ID Broker](identity-broker.md) během vytváření clusteru. Funkce ID Broker umožňuje přihlásit se k Ambari pomocí vícefaktorového ověřování a získat požadované lístky protokolu Kerberos bez nutnosti hash hesel ve službě Azure AD DS.

> [!NOTE]  
> Prvních šest znaků názvů clusterů ESP musí být ve vašem prostředí jedinečných. Pokud máte například více clusterů ESP v různých virtuálních sítích, zvolte konvenci pojmenování, která zajistí, že prvních šest znaků v názvech clusterů bude jedinečných.

![Ověření domény pro balíček zabezpečení Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Po povolení protokolu ESP se automaticky zhlosnou a ověří běžné chybné konfigurace související s Azure AD DS. Po opravě těchto chyb můžete pokračovat dalším krokem.

![Ověření domény balíčku podnikového zabezpečení Azure HDInsight se nezdařilo](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Při vytváření clusteru HDInsight s ESP je nutné zadat následující parametry:

- **Uživatel správce clusteru:** Zvolte správce pro váš cluster ze synchronizované instance Azure AD DS. Tento účet domény musí být už synchronizovaný a dostupný ve službě Azure AD DS.

- **Skupiny přístupu ke clusteru**: Skupiny zabezpečení, jejichž uživatelé, které chcete synchronizovat a mají přístup ke clusteru, by měly být dostupné ve službě Azure AD DS. Příkladem je skupina HiveUsers. Další informace najdete [v tématu Vytvoření skupiny a přidání členů ve službě Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **ADRESA URL PROTOKOLU LDAPS**: Příkladem je `ldaps://contoso.com:636`.

Vytvořenou spravovanou identitu lze vybrat z rozevíracího seznamu **spravované identity přiřazené uživatelem** při vytváření nového clusteru.

![Spravovaná identita služby Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci zásad Hive a spuštění dotazů Hive naleznete [v tématu Konfigurace zásad Apache Hive pro clustery HDInsight pomocí protokolu ESP](apache-domain-joined-run-hive.md).
* Informace o použití SSH pro připojení ke clusterům HDInsight pomocí ESP najdete v tématu [Použití SSH s Apache Hadoop založeným na Linuxu na HDInsightu z Linuxu, Unixu nebo OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
