---
title: Vytvoření a konfigurace clusterů Balíček zabezpečení podniku ve službě Azure HDInsight
description: Naučte se vytvářet a konfigurovat clustery Balíček zabezpečení podniku ve službě Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 9b9071eae4ec18cb1d5fd277f6f5403ce3997f48
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261739"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Vytvoření a konfigurace clusterů Balíček zabezpečení podniku ve službě Azure HDInsight

Balíček zabezpečení podniku (ESP) pro Azure HDInsight poskytuje přístup k ověřování založenému na službě Active Directory, víceuživatelské podpoře a řízení přístupu na základě rolí pro vaše clustery Apache Hadoop v Azure. Clustery HDInsight ESP umožňují organizacím, které vyhovují přísným podnikovým zásadám zabezpečení pro bezpečné zpracování citlivých dat.

V této příručce se dozvíte, jak vytvořit cluster Azure HDInsight s povoleným protokolem ESP. Také ukazuje, jak vytvořit virtuální počítač s Windows IaaS, ve kterém jsou povolené služby Active Directory a DNS (Domain Name System). Tato příručka slouží ke konfiguraci nezbytných prostředků, které umožní místním uživatelům přihlašovat se ke clusteru HDInsight s povoleným protokolem ESP.

Server, který vytvoříte, bude sloužit jako náhrada za vaše *skutečné* místní prostředí. Použijete ji pro kroky nastavení a konfigurace. Později zopakujete kroky ve svém vlastním prostředí. 

Tento průvodce vám také pomůže vytvořit hybridní prostředí identity pomocí synchronizace hodnot hash hesel pomocí Azure Active Directory (Azure AD). Tato příručka doplňuje [používání protokolu ESP v HDInsight](apache-domain-joined-architecture.md).

Než použijete tento proces ve vašem vlastním prostředí:
* Nastavte službu Active Directory a DNS.
* Povolte službu Azure AD.
* Synchronizace místních uživatelských účtů s Azure AD.

![Diagram architektury Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Vytvoření místního prostředí

V této části použijete šablonu nasazení Azure pro rychlý Start k vytvoření nových virtuálních počítačů, konfiguraci DNS a přidání nové doménové struktury služby Active Directory.

1. Pokud chcete [vytvořit virtuální počítač Azure s novou doménovou strukturou služby Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), můžete přejít na šablonu nasazení pro rychlý Start.

1. Vyberte **nasadit do Azure**.
1. Přihlaste se ke svému předplatnému Azure.
1. Na stránce **vytvořit virtuální počítač Azure s novou stránkou doménové struktury služby AD** :
    1. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém chcete prostředky nasadit.
    1. Vedle pole **Skupina prostředků**vyberte **vytvořit novou**a zadejte název *OnPremADVRG*.
    1. Pro zbytek polí šablony zadejte následující podrobnosti:

        * **Umístění**: Střed USA
        * **Uživatelské jméno správce**: HDIFabrikamAdmin
        * **Heslo správce**: \<YOUR_PASSWORD >
        * **Název domény**: HDIFabrikam.com
        * **Předpona DNS**: hdifabrikam

        ![Šablona pro vytvoření virtuálního počítače Azure s novou doménovou strukturou Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Vyberte **Koupit**.
    1. Monitorujte nasazení a počkejte na jeho dokončení.
    1. Zajistěte, aby se prostředky vytvořily v rámci správné skupiny prostředků **OnPremADVRG**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurace uživatelů a skupin pro přístup k clusteru

V této části vytvoříte uživatele, kteří budou mít přístup ke clusteru HDInsight na konci této příručky.

1. Připojte se k řadiči domény pomocí vzdálené plochy.
    1. Pokud jste použili šablonu uvedenou na začátku, řadič domény je virtuálním počítačem s názvem **adVM** ve skupině prostředků **OnPremADVRG** .
    1. V Azure Portal vyberte **skupiny** > prostředků**OnPremADVRG** > **adVM** > **připojit**.
    1. Vyberte kartu **rdp** > **Stáhnout soubor RDP**.
    1. Uložte soubor do počítače a otevřete ho.
    1. Po zobrazení výzvy k zadání přihlašovacích údajů použijte jako uživatelské jméno *HDIFabrikam\HDIFabrikamAdmin* . Pak zadejte heslo, které jste zvolili pro účet správce.

1. Po otevření relace vzdálené plochy na VIRTUÁLNÍm počítači s řadičem domény otevřete na řídicím panelu **Správce serveru** modul **Uživatelé a počítače služby Active Directory**. V pravém horním rohu vyberte **nástroje** > **Uživatelé a počítače služby Active Directory**.

    ![Na řídicím panelu Správce serveru otevřete správu služby Active Directory.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Vytvořte dva nové uživatele: **HDIAdmin** a **HDIUser**. Tito dva uživatelé se budou přihlašovat ke clusterům HDInsight.

    1. Na stránce **Uživatelé a počítače služby Active Directory** vyberte **Akce** > **Nový** > **uživatel**.

        ![Vytvořit nového uživatele služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na stránce **Nový objekt – uživatel** zadejte do **přihlašovacího jména uživatele** *HDIUser*. Pak vyberte **Další**.

        ![Vytvoření prvního objektu uživatele správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. V automaticky otevíraném okně, které se zobrazí, zadejte heslo pro nový účet. Vyberte **heslo** > je stále v**pořádku**.
    1. Kliknutím na **Dokončit** vytvořte nový účet.
    1. Vytvořte dalšího uživatele s názvem *HDIAdmin*.

        ![Vytvoření druhého objektu uživatele správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Na stránce **Uživatelé a počítače služby Active Directory** vyberte **Akce** > **Nová** > **Skupina**. Přidejte skupinu s názvem *HDIUserGroup*.

    ![Vytvoření nové skupiny služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Vytvořit nový objekt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Přidejte **HDIUser** do **HDIUserGroup** jako člena skupiny.

    1. Klikněte pravým tlačítkem na **HDIUserGroup** a vyberte **vlastnosti**.
    1. Na kartě **Členové** vyberte **Přidat**.
    1. Do pole **Zadejte názvy objektů k výběru** zadejte *HDIUser*. Pak vyberte **OK**.
    1. Opakujte předchozí postup pro účet **HDIAdmin** .

        ![Přidat člena HDIUser do skupiny HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nyní jste vytvořili prostředí Active Directory. Přidali jste dva uživatele a skupinu uživatelů, kteří mají přístup ke clusteru HDInsight.

Uživatelé budou synchronizováni se službou Azure AD.

### <a name="create-an-azure-ad-directory"></a>Vytvoření adresáře Azure AD

1. Přihlaste se k portálu Azure.
1. Vyberte **vytvořit prostředek** a zadejte *adresář*. Vyberte **Azure Active Directory** > **vytvořit**.
1. Do **pole název organizace**zadejte *HDIFabrikam*.
1. V části **počáteční název domény**zadejte *HDIFabrikamoutlook*.
1. Vyberte **Vytvořit**.

    ![Vytvoření adresáře Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Na levé straně Azure Portal vyberte možnost **Azure Active Directory**.
1. V případě potřeby vyberte **Přepnout adresář** a přejděte do nového adresáře **HDIFabrikamoutlook** .
1. V části **Spravovat**vyberte **vlastní názvy** > domén**Přidat vlastní doménu**.
1. V části **vlastní název domény**zadejte *HDIFabrikam.com* a vyberte **Přidat doménu**.

![Vytvoření vlastní domény](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurace tenanta Azure AD

Teď nakonfigurujete tenanta Azure AD tak, aby bylo možné synchronizovat uživatele a skupiny z místní instance služby Active Directory do cloudu.

1. Vytvořte správce tenanta služby Active Directory.
    1. Přihlaste se k Azure Portal a vyberte svého tenanta Azure AD **HDIFabrikam**.
    1. V části **Spravovat**vyberte **Uživatelé** > **Nový uživatel**.
    1. Pro nového uživatele zadejte následující podrobnosti:
        * **Název**: fabrikamazureadmin
        * **Uživatelské jméno**:fabrikamazureadmin@hdifabrikam.com
        * **Heslo**: Zabezpečené heslo podle vašeho výběru

    1. V části **skupiny** vyhledejte **správce AAD řadiče domény** a klikněte na **Vybrat**.

        ![Dialogové okno skupiny Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Otevřete oddíl **role adresáře** a na pravé straně vyberte **globální správce** > **OK**.

        ![Dialogové okno role Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Zadejte heslo pro uživatele. Potom vyberte **Vytvořit**.

1. Pokud chcete změnit heslo nově vytvořeného uživatele \< fabrikamazureadmin@hdifabrikam.com>, přihlaste se k Azure Portal pomocí identity. Pak se zobrazí výzva ke změně hesla.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizace místních uživatelů s Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Stažení a instalace Azure AD Connect

1. [Stáhněte si Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Nainstalujte Azure AD Connect na řadič domény.

    1. Otevřete spustitelný soubor, který jste stáhli, a vyjádřete souhlas s licenčními podmínkami. Vyberte **pokračovat**.

        ![Stránka Vítá vás Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Vyberte **Použít expresní nastavení** a dokončete instalaci.

        ![Nastavení expresního Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Konfigurace synchronizace s místním řadičem domény

1. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo globálního správce služby Azure AD. Použijte uživatelské jméno `fabrikamazureadmin@hdifabrikam.com` , které jste vytvořili při konfiguraci tenanta služby Active Directory. Pak vyberte **Další**. 

    ![Stránka připojení ke službě Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na stránce **připojit k Active Directory Domain Services** zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Použijte uživatelské jméno `HDIFabrikam\HDIFabrikamAdmin` a heslo, které jste vytvořili dříve. Pak vyberte **Další**. 

   ![Stránka připojení ke službě Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stránce **Konfigurace přihlášení k Azure AD** vyberte **Další**.
   ![Stránka Konfigurace přihlášení k Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na stránce **připraveno ke konfiguraci** vyberte **instalovat**.

   ![Stránka připraveno ke konfiguraci](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na stránce **Konfigurace byla dokončena** vyberte možnost **ukončit**.
   ![Stránka "konfigurace dokončena"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po dokončení synchronizace potvrďte, že uživatelé, které jste vytvořili v adresáři IaaS, se synchronizují do Azure AD.
   1. Přihlaste se k portálu Azure.
   1. Vyberte **Azure Active Directory** > **Uživatelé** **HDIFabrikam** > .

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte spravovanou identitu přiřazenou uživatelem, kterou můžete použít ke konfiguraci Azure AD Domain Services (Azure služba AD DS). Další informace najdete v tématu [Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Přihlaste se k portálu Azure.
1. Vyberte **vytvořit prostředek** a zadejte *spravovanou identitu*. Vyberte možnost > **vytvořit** **spravovanou identitu přiřazenou uživatelem**.
1. Jako **název prostředku**zadejte *HDIFabrikamManagedIdentity*.
1. Vyberte své předplatné.
1. V části **Skupina prostředků**vyberte **vytvořit novou** a zadejte *HDIFabrikam-CentralUS*.
1. V části **umístění**vyberte **střed USA**.
1. Vyberte **Vytvořit**.

![vytvořit novou spravovanou identitu přiřazenou uživatelem](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Povolit Azure služba AD DS

Pomocí následujícího postupu povolíte Azure služba AD DS. Další informace najdete v tématu [Povolení služby Azure služba AD DS pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Vytvořte virtuální síť pro hostování Azure služba AD DS. Spusťte následující kód PowerShellu.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Přihlaste se k portálu Azure.
1. Vyberte **vytvořit prostředek**, zadejte *Domain services*a vyberte **Azure AD Domain Services**.
1. Na stránce **základy** :
    1. V části **název adresáře**vyberte adresář služby Azure AD, který jste vytvořili: **HDIFabrikam**.
    1. Jako **název domény DNS**zadejte *HDIFabrikam.com*.
    1. Vyberte své předplatné.
    1. Zadejte skupinu prostředků **HDIFabrikam-CentralUS**. V **oblasti umístění**vyberte **střed USA**.

        ![Podrobnosti o Azure služba AD DS základní](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na stránce **síť** vyberte síť (**HDIFabrikam-VNet**) a podsíť (**AADDS-Subnet**), kterou jste vytvořili pomocí skriptu PowerShellu. Pokud teď chcete vytvořit virtuální síť, vyberte **vytvořit novou** .

    ![Krok vytvořit virtuální síť](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na stránce **skupina správců** by se měla zobrazit oznámení o tom, že pro správu této skupiny už se vytvořila skupina s názvem **Správci AAD DC** . Členství v této skupině můžete upravit, pokud chcete, ale v takovém případě ji nemusíte měnit. Vyberte **OK**.

    ![Zobrazit skupinu správce Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na stránce **synchronizace** povolte kompletní synchronizaci výběrem možnosti **vše** > **OK**.

    ![Povolit synchronizaci se službou Azure služba AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na stránce **Souhrn** zkontrolujte podrobnosti pro Azure služba AD DS a vyberte **OK**.

    ![Souhrn příkazu "Enable Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Po povolení služby Azure služba AD DS bude místní server DNS spuštěný na virtuálních počítačích Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurace virtuální sítě Azure služba AD DS

Pomocí následujících kroků můžete nakonfigurovat službu Azure služba AD DS Virtual Network (**HDIFabrikam-AADDSVNET**), aby používala vlastní servery DNS.

1. Vyhledejte IP adresy vašich vlastních serverů DNS. 
    1. Vyberte prostředek **HDIFabrikam.com** Azure služba AD DS. 
    1. V části **Spravovat**vyberte **vlastnosti**. 
    1. Vyhledá IP adresy v části **IP adresa ve virtuální síti**.

    ![Vyhledání vlastních IP adres DNS pro Azure služba AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Nakonfigurujte **HDIFabrikam-AADDSVNET** , aby používaly vlastní IP adresy 10.0.0.4 a 10.0.0.5.

    1. V části **Nastavení**vyberte **servery DNS**. 
    1. Vyberte možnost **vlastní**.
    1. Do textového pole zadejte první IP adresu (*10.0.0.4*).
    1. Vyberte **Uložit**.
    1. Zopakováním kroků přidejte druhou IP adresu (*10.0.0.5*).

V našem scénáři jsme nakonfigurovali služba AD DS pro Azure, aby používaly IP adresy 10.0.0.4 a 10.0.0.5, a nastavení stejné IP adresy ve službě Azure služba AD DS Virtual Network:

![Stránka vlastní servery DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpečení provozu protokolu LDAP

Protokol LDAP (Lightweight Directory Access Protocol) se používá ke čtení a zápisu do Azure Active Directory. Komunikaci s protokolem LDAP můžete chránit a zabezpečit pomocí technologie SSL (Secure Sockets Layer) (SSL) nebo technologie TLS (Transport Layer Security). Pomocí protokolu LDAP přes SSL (LDAPs) můžete povolit instalaci správně formátovaného certifikátu.

Další informace o zabezpečeném protokolu LDAP najdete v tématu [Konfigurace LDAPS pro spravovanou doménu Azure služba AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

V této části vytvoříte certifikát podepsaný svým držitelem, stáhnete certifikát a nakonfigurujete LDAPs pro **HDIFabrikam** Azure služba AD DS spravované domény.

Následující skript vytvoří certifikát pro **HDIFabrikam**. Certifikát je uložený v cestě *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> K vytvoření žádosti o certifikát SSL se dá použít jakýkoli nástroj nebo aplikace, které \#vytvoří platný požadavek PKCS (Public Key Cryptography Standards) 10.

Ověřte, zda je certifikát nainstalován v **osobním** úložišti počítače:

1. Spusťte konzolu MMC (Microsoft Management Console).
1. Přidejte modul snap-in **certifikáty** , který spravuje certifikáty v místním počítači.
1. Rozbalte položku **certifikáty (místní počítač)**  > **osobní** > **certifikáty**. V **osobním** úložišti by měl existovat nový certifikát. Tento certifikát je vydaný plně kvalifikovanému názvu hostitele.

    ![Ověřit vytvoření místního certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. V pravém podokně klikněte pravým tlačítkem na certifikát, který jste vytvořili. Přejděte na **všechny úlohy**a pak vyberte **exportovat**.

1. Na stránce **exportovat soukromý klíč** vyberte **Ano, exportovat soukromý klíč**. Počítač, do kterého se klíč importuje, potřebuje k přečtení šifrovaných zpráv privátní klíč.

    ![Stránka export privátního klíče Průvodce exportem certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stránce **Formát** souboru pro export ponechte výchozí nastavení a pak vyberte **Další**. 
1. Na stránce **heslo** zadejte heslo pro privátní klíč. V případě **šifrování**vyberte možnost **TripleDES-SHA1**. Pak vyberte **Další**.
1. Na stránce **soubor pro export** zadejte cestu a název exportovaného souboru certifikátu a pak vyberte **Další**. Název souboru musí mít příponu. pfx. Tento soubor je nakonfigurovaný v Azure Portal, aby se navázalo zabezpečené připojení.
1. Povoluje protokol LDAPs pro spravovanou doménu Azure služba AD DS.
    1. Z Azure Portal vyberte **HDIFabrikam.com**domény.
    1. V části **Spravovat**vyberte **protokol Secure LDAP**.
    1. Na stránce **protokol Secure LDAP** v části **protokol Secure LDAP**vyberte **Povolit**.
    1. Vyhledejte soubor certifikátu. pfx, který jste exportovali v počítači.
    1. Zadejte heslo certifikátu.

    ![Povolit zabezpečený protokol LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Teď, když jste povolili LDAPs, se ujistěte, že je dosažitelná povolením portu 636.
    1. Ve skupině prostředků **HDIFabrikam-CentralUS** vyberte skupinu zabezpečení sítě **AADDS-HDIFabrikam.com-NSG**.
    1. V části **Nastavení**vyberte **příchozí pravidla** > zabezpečení**Přidat**.
    1. Na stránce **Přidat pravidlo zabezpečení příchozího připojení** zadejte následující vlastnosti a vyberte **Přidat**:

        | Vlastnost | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Požadované číslo > |
        | Name | Port_LDAP_636 |

    ![Dialogové okno Přidat příchozí pravidlo zabezpečení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** je spravovaná identita přiřazená uživatelem. Role přispěvatele služby HDInsight Domain Services je povolená pro spravovanou identitu, která umožní této identitě číst, vytvářet, upravovat a odstraňovat operace služby Domain Services.

![Vytvoření spravované identity přiřazené uživatelem](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Vytvoření clusteru HDInsight s povoleným protokolem ESP

Tento krok vyžaduje následující požadavky:

1. Vytvořte novou skupinu prostředků *HDIFabrikam-WestUS* v umístění **západní USA**.
1. Vytvořte virtuální síť, která bude hostovat cluster HDInsight s podporou protokolu ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Vytvořte partnerský vztah mezi virtuální sítí, která je hostitelem služby Azure služba AD DS`HDIFabrikam-AADDSVNET`() a virtuální sítě, která bude hostovat cluster HDInsight s podporou protokolu ESP`HDIFabrikam-HDIVNet`(). K navázání partnerského vztahu mezi dvěma virtuálními sítěmi použijte následující kód PowerShellu.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Vytvořte nový účet Azure Data Lake Storage Gen2 s názvem **Hdigen2store**. Nakonfigurujte účet pomocí uživatelsky spravované identity **HDIFabrikamManagedIdentity**. Další informace najdete v tématu [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Nastavte vlastní DNS ve virtuální síti **HDIFabrikam-AADDSVNET** .
    1. Do **skupin** > prostředků služby Azure Portal >**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **servery DNS**.
    1. Vyberte **vlastní** a zadejte *10.0.0.4* a *10.0.0.5*.
    1. Vyberte **Uložit**.

        ![Uložení vlastních nastavení DNS pro virtuální síť](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Vytvořte nový cluster HDInsight Spark s povoleným protokolem ESP.
    1. Vyberte **vlastní (velikost, nastavení, aplikace)** .
    2. Zadejte podrobnosti o **základech** (oddíl 1). Zajistěte, aby byl **cluster typu** **Spark 2,3 (HDI 3,6)** . Ujistěte se, že je **Skupina prostředků** **HDIFabrikam-CentralUS**.

    1. Pro **zabezpečení a sítě** (část 2) vyplňte následující podrobnosti:
        * V části **balíček zabezpečení podniku**vyberte **povoleno**.
        * Vyberte možnost **uživatel správce clusteru** a vyberte účet **HDIAdmin** , který jste vytvořili jako místní uživatel s oprávněními správce. Klikněte na tlačítko **vyberte**.
        *  > Vyberte **skupinu přístupu clusteru** **HDIUserGroup**. Každý uživatel, který přidáte do této skupiny v budoucnosti, bude mít přístup k clusterům HDInsight.

            ![Vyberte skupinu přístupu clusteru HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Proveďte další kroky konfigurace clusteru a ověřte podrobnosti o **souhrnu clusteru**. Vyberte **Vytvořit**.

1. Přihlaste se k uživatelskému rozhraní Ambari pro nově vytvořený `https://CLUSTERNAME.azurehdinsight.net`cluster v. Použijte uživatelské jméno `hdiadmin@hdifabrikam.com` správce a jeho heslo.

    ![Přihlašovací okno uživatelského rozhraní Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Z řídicího panelu clusteru vyberte **role**.
1. Na stránce **role** v části **přiřadit role k těmto**položkám vedle role **Správce clusteru** zadejte skupinu *hdiusergroup*. 

    ![Přiřazení role Správce clusteru k hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otevřete klienta Secure Shell (SSH) a přihlaste se ke clusteru. Použijte **hdiuser** , který jste vytvořili v místní instanci služby Active Directory.

    ![Přihlaste se ke clusteru pomocí klienta SSH.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Pokud se k tomuto účtu můžete přihlásit, nakonfigurovali jste cluster ESP správně, aby se synchronizovala s místní instancí Active Directory.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Úvod k zabezpečení Apache Hadoop pomocí protokolu ESP](hdinsight-security-overview.md).
