---
title: Vytvoření, konfigurace Balíček zabezpečení podniku clusterů – Azure
description: Naučte se vytvářet a konfigurovat clustery Balíček zabezpečení podniku ve službě Azure HDInsight.
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 3e6a5742d2dc8079c36f2d9f39a827f8db99cd67
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867164"
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

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png" alt-text="Diagram architektury Azure AD" border="false":::

## <a name="create-an-on-premises-environment"></a>Vytvoření místního prostředí

V této části použijete šablonu nasazení Azure pro rychlý Start k vytvoření nových virtuálních počítačů, konfiguraci DNS a přidání nové doménové struktury služby Active Directory.

1. Pokud chcete [vytvořit virtuální počítač Azure s novou doménovou strukturou služby Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), můžete přejít na šablonu nasazení pro rychlý Start.

1. Vyberte **nasadit do Azure**.
1. Přihlaste se ke svému předplatnému Azure.
1. Na stránce **vytvořit virtuální počítač Azure s novou doménovou strukturou služby AD** zadejte tyto informace:

    |Vlastnost | Hodnota |
    |---|---|
    |Předplatné|Vyberte předplatné, ve kterém chcete prostředky nasadit.|
    |Skupina prostředků|Vyberte **vytvořit novou** a zadejte název. `OnPremADVRG`|
    |Umístění|Vyberte umístění.|
    |Uživatelské jméno správce|`HDIFabrikamAdmin`|
    |Heslo správce|Zadejte heslo.|
    |Název domény|`HDIFabrikam.com`|
    |Předpona DNS|`hdifabrikam`|

    Zbývající výchozí hodnoty ponechte.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png" alt-text="Šablona pro vytvoření virtuálního počítače Azure s novou doménovou strukturou Azure AD" border="true":::

1. Přečtěte si **podmínky a ujednání** a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.
1. Vyberte **koupit** a sledujte nasazení a počkejte, než se dokončí. Dokončení nasazení trvá přibližně 30 minut.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurace uživatelů a skupin pro přístup k clusteru

V této části vytvoříte uživatele, kteří budou mít přístup ke clusteru HDInsight na konci této příručky.

1. Připojte se k řadiči domény pomocí vzdálené plochy.
    1. V Azure Portal přejděte na **skupiny prostředků**  >  **OnPremADVRG**  >  **adVM**  >  **připojit**.
    1. V rozevíracím seznamu **IP adresa** vyberte veřejná IP adresa.
    1. Vyberte **Stáhnout soubor RDP** a pak soubor otevřete.
    1. Použijte `HDIFabrikam\HDIFabrikamAdmin` jako uživatelské jméno.
    1. Zadejte heslo, které jste zvolili pro účet správce.
    1. Vyberte **OK**.

1. Z řadiče domény **Správce serveru** řídicím panelu přejděte na **nástroje**  >  **Uživatelé a počítače služby Active Directory**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png" alt-text="Na řídicím panelu Správce serveru otevřete správu služby Active Directory." border="true":::

1. Vytvořte dva nové uživatele: **HDIAdmin** a **HDIUser**. Tito dva uživatelé se budou přihlašovat ke clusterům HDInsight.

    1. Na stránce **Uživatelé a počítače služby Active Directory** klikněte pravým tlačítkem myši `HDIFabrikam.com` a potom přejděte k **novému**  >  **uživateli**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png" alt-text="Vytvořit nového uživatele služby Active Directory" border="true":::

    1. Na stránce **Nový objekt – uživatel** zadejte pro jméno `HDIUser` a  **přihlašovací jméno uživatele**. Ostatní pole se vyplní automaticky. Pak vyberte **Další**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png" alt-text="Vytvoření prvního objektu uživatele správce" border="true":::

    1. V automaticky otevíraném okně, které se zobrazí, zadejte heslo pro nový účet. Vyberte **heslo je platné** stále a v místní zprávě klikněte na **OK** .
    1. Vyberte **Další** a potom **Dokončit** pro vytvoření nového účtu.
    1. Opakujte výše uvedené kroky a vytvořte uživatele `HDIAdmin` .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png" alt-text="Vytvoření druhého objektu uživatele správce" border="true":::

1. Vytvořte globální skupinu zabezpečení.

    1. V části **Uživatelé a počítače služby Active Directory** klikněte pravým tlačítkem myši `HDIFabrikam.com` a potom přejděte do **nové**  >  **skupiny**.

    1. `HDIUserGroup`Do textového pole **název skupiny** zadejte.

    1. Vyberte **OK**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png" alt-text="Vytvoření nové skupiny služby Active Directory" border="true":::

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png" alt-text="Vytvořit nový objekt" border="true":::

1. Přidejte členy do **HDIUserGroup**.

    1. Klikněte pravým tlačítkem na **HDIUser** a vyberte **Přidat do skupiny...**.
    1. Do textového pole **Zadejte názvy objektů k výběru** zadejte `HDIUserGroup` . Pak vyberte **OK** a znovu **OK** v automaticky otevíraném okně.
    1. Opakujte předchozí postup pro účet **HDIAdmin** .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png" alt-text="Přidat člena HDIUser do skupiny HDIUserGroup" border="true":::

Nyní jste vytvořili prostředí Active Directory. Přidali jste dva uživatele a skupinu uživatelů, kteří mají přístup ke clusteru HDInsight.

Uživatelé budou synchronizováni se službou Azure AD.

### <a name="create-an-azure-ad-directory"></a>Vytvoření adresáře Azure AD

1. Přihlaste se k webu Azure Portal.
1. Vyberte **vytvořit prostředek** a typ `directory` . Vyberte **Azure Active Directory**  >  **vytvořit**.
1. Do **pole název organizace** zadejte `HDIFabrikam` .
1. V části **počáteční název domény** zadejte `HDIFabrikamoutlook` .
1. Vyberte **Vytvořit**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png" alt-text="Vytvoření adresáře Azure AD" border="true":::

### <a name="create-a-custom-domain"></a>Vytvoření vlastní domény

1. V novém **Azure Active Directory** v části **Spravovat** vyberte **vlastní názvy domén**.
1. Vyberte **+ Přidat vlastní doménu**.
1. V části **vlastní název domény** zadejte `HDIFabrikam.com` a pak vyberte **Přidat doménu**.
1. Potom dokončete [Přidání informací DNS do doménového registrátora](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png" alt-text="Vytvoření vlastní domény" border="true":::

### <a name="create-a-group"></a>Vytvoření skupiny

1. V novém **Azure Active Directory** v části **Spravovat** vyberte **skupiny**.
1. Vyberte **+ Nová skupina**.
1. Do textového pole **název skupiny** zadejte `AAD DC Administrators` .
1. Vyberte **Vytvořit**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurace tenanta Azure AD

Teď nakonfigurujete tenanta Azure AD tak, aby bylo možné synchronizovat uživatele a skupiny z místní instance služby Active Directory do cloudu.

Vytvořte správce tenanta služby Active Directory.

1. Přihlaste se k Azure Portal a vyberte svého tenanta Azure AD **HDIFabrikam**.

1. Přejděte na **Spravovat**  >  **uživatele**–  >  **Nový uživatel**.

1. Pro nového uživatele zadejte následující podrobnosti:

   **Identita**

   |Vlastnost |Popis |
   |---|---|
   |Uživatelské jméno|`fabrikamazureadmin`Do textového pole zadejte. V rozevíracím seznamu název domény vyberte `hdifabrikam.com`|
   |Name| Zadejte `fabrikamazureadmin`.|

   **Heslo**
   1. Vyberte možnost **Chci vytvořit heslo**.
   1. Zadejte zabezpečené heslo podle vašeho výběru.

   **Skupiny a role**
   1. Vyberte **0 vybraných skupin**.
   1. Vyberte **správce AAD řadiče domény** a pak **Vyberte**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png" alt-text="Dialogové okno skupiny Azure AD" border="true":::

   1. Vyberte možnost **uživatel**.
   1. Vyberte **globální správce** a pak **Vyberte**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png" alt-text="Dialogové okno role Azure AD" border="true":::

1. Vyberte **Vytvořit**.

1. Pak se nový uživatel přihlásí k Azure Portal, kde se zobrazí výzva ke změně hesla. To je nutné provést před konfigurací Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizace místních uživatelů s Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurace Microsoft Azure Active Directory Connect

1. Z řadiče domény stáhněte [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Otevřete spustitelný soubor, který jste stáhli, a vyjádřete souhlas s licenčními podmínkami. Vyberte **Pokračovat**.

1. Vyberte **Použít expresní nastavení**.

1. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo globálního správce služby Azure AD. Použijte uživatelské jméno `fabrikamazureadmin@hdifabrikam.com` , které jste vytvořili při konfiguraci tenanta služby Active Directory. Pak vyberte **Další**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png" alt-text="Připojení k Azure A D" border="true":::

1. Na stránce **připojit k Active Directory Domain Services** zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Použijte uživatelské jméno `HDIFabrikam\HDIFabrikamAdmin` a heslo, které jste vytvořili dříve. Pak vyberte **Další**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png" alt-text="Připojte se ke stránce D D S." border="true":::

1. Na stránce **Konfigurace přihlášení k Azure AD** vyberte **Další**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png" alt-text="Konfigurační stránka pro přihlášení ke službě Azure AD" border="true":::

1. Na stránce **připraveno ke konfiguraci** vyberte **instalovat**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png" alt-text="Připraveno ke konfiguraci stránky" border="true":::

1. Na stránce **Konfigurace byla dokončena** vyberte možnost **ukončit**.
   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png" alt-text="Stránka dokončení konfigurace" border="true":::

1. Po dokončení synchronizace potvrďte, že uživatelé, které jste vytvořili v adresáři IaaS, se synchronizují do Azure AD.
   1. Přihlaste se k webu Azure Portal.
   1. Vyberte **Azure Active Directory**  >    >  **Uživatelé** HDIFabrikam.

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte spravovanou identitu přiřazenou uživatelem, kterou můžete použít ke konfiguraci Azure AD Domain Services (Azure služba AD DS). Další informace najdete v tématu [Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Přihlaste se k webu Azure Portal.
1. Vyberte **vytvořit prostředek** a typ `managed identity` . Vyberte možnost vytvořit **spravovanou identitu přiřazenou uživatelem**  >  .
1. Jako **název prostředku** zadejte `HDIFabrikamManagedIdentity` .
1. Vyberte své předplatné.
1. V části **Skupina prostředků** vyberte **vytvořit nové** a zadejte `HDIFabrikam-CentralUS` .
1. V části **umístění** vyberte **střed USA**.
1. Vyberte **Vytvořit**.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png" alt-text="Vytvořit novou spravovanou identitu přiřazenou uživatelem" border="true":::

### <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Pomocí následujícího postupu povolíte Azure služba AD DS. Další informace najdete v tématu [Povolení služby Azure služba AD DS pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

1. Vytvořte virtuální síť pro hostování Azure služba AD DS. Spusťte následující kód PowerShellu.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Přihlaste se k webu Azure Portal.
1. Vyberte **vytvořit prostředek**, zadejte `Domain services` a vyberte **Azure AD Domain Services**  >  **vytvořit**.
1. Na stránce **základy** :
   1. V části **název adresáře** vyberte adresář služby Azure AD, který jste vytvořili: **HDIFabrikam**.
   1. Jako **název domény DNS** zadejte *HDIFabrikam.com*.
   1. Vyberte své předplatné.
   1. Zadejte skupinu prostředků **HDIFabrikam-CentralUS**. V **oblasti umístění** vyberte **střed USA**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png" alt-text="Podrobnosti o Azure služba AD DS základní" border="true":::

1. Na stránce **síť** vyberte síť (**HDIFabrikam-VNet**) a podsíť (**AADDS-Subnet**), kterou jste vytvořili pomocí skriptu PowerShellu. Pokud teď chcete vytvořit virtuální síť, vyberte **vytvořit novou** .

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png" alt-text="Krok vytvoření virtuální sítě" border="true":::

1. Na stránce **skupina správců** by se měla zobrazit oznámení o tom, že pro správu této skupiny už se vytvořila skupina s názvem **Správci AAD DC** . Členství v této skupině můžete upravit, pokud chcete, ale v takovém případě ji nemusíte měnit. Vyberte **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png" alt-text="Zobrazit skupinu správce Azure AD" border="true":::

1. Na stránce **synchronizace** povolte kompletní synchronizaci výběrem možnosti **vše**  >  **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png" alt-text="Povolit synchronizaci se službou Azure služba AD DS" border="true":::

1. Na stránce **Souhrn** zkontrolujte podrobnosti pro Azure služba AD DS a vyberte **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png" alt-text="Povolení služby Azure AD Domain Services" border="true":::

Po povolení služby Azure služba AD DS bude místní server DNS spuštěný na virtuálních počítačích Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurace virtuální sítě Azure služba AD DS

Pomocí následujících kroků můžete nakonfigurovat službu Azure služba AD DS Virtual Network (**HDIFabrikam-AADDSVNET**), aby používala vlastní servery DNS.

1. Vyhledejte IP adresy vašich vlastních serverů DNS.
   1. Vyberte `HDIFabrikam.com` prostředek Azure služba AD DS.
   1. V části **Spravovat** vyberte **Vlastnosti**.
   1. Vyhledá IP adresy v části **IP adresa ve virtuální síti**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png" alt-text="Vyhledání vlastních IP adres DNS pro Azure služba AD DS" border="true":::

1. Nakonfigurujte **HDIFabrikam-AADDSVNET** , aby používaly vlastní IP adresy 10.0.0.4 a 10.0.0.5.

   1. V části **Nastavení** vyberte **servery DNS**.
   1. Vyberte možnost **vlastní**.
   1. Do textového pole zadejte první IP adresu (*10.0.0.4*).
   1. Vyberte **Uložit**.
   1. Zopakováním kroků přidejte druhou IP adresu (*10.0.0.5*).

V našem scénáři jsme nakonfigurovali služba AD DS pro Azure, aby používaly IP adresy 10.0.0.4 a 10.0.0.5, a nastavení stejné IP adresy ve službě Azure služba AD DS Virtual Network:

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png" alt-text="Stránka vlastní servery DNS" border="true":::

## <a name="securing-ldap-traffic"></a>Zabezpečení provozu protokolu LDAP

Protokol LDAP (Lightweight Directory Access Protocol) se používá ke čtení a zápisu do Azure Active Directory. Komunikaci s protokolem LDAP můžete chránit a zabezpečit pomocí technologie SSL (Secure Sockets Layer) (SSL) nebo technologie TLS (Transport Layer Security). Pomocí protokolu LDAP přes SSL (LDAPs) můžete povolit instalaci správně formátovaného certifikátu.

Další informace o zabezpečeném protokolu LDAP najdete v tématu [Konfigurace LDAPS pro spravovanou doménu Azure služba AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

V této části vytvoříte certifikát podepsaný svým držitelem, stáhnete certifikát a nakonfigurujete LDAPs pro **HDIFabrikam** Azure služba AD DS spravované domény.

Následující skript vytvoří certifikát pro **HDIFabrikam**. Certifikát je uložený v cestě *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Libovolný nástroj nebo aplikace, které vytvoří platný požadavek PKCS (Public Key Cryptography Standards) \# 10, lze použít k vytvoření žádosti o certifikát TLS/SSL.

Ověřte, zda je certifikát nainstalován v **osobním** úložišti počítače:

1. Spusťte konzolu MMC (Microsoft Management Console).
1. Přidejte modul snap-in **certifikáty** , který spravuje certifikáty v místním počítači.
1. Rozbalte položku **certifikáty (místní počítač)**  >  **osobní**  >  **certifikáty**. V **osobním** úložišti by měl existovat nový certifikát. Tento certifikát je vydaný plně kvalifikovanému názvu hostitele.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png" alt-text="Ověřit vytvoření místního certifikátu" border="true":::

1. V pravém podokně klikněte pravým tlačítkem na certifikát, který jste vytvořili. Přejděte na **všechny úlohy** a pak vyberte **exportovat**.

1. Na stránce **exportovat soukromý klíč** vyberte **Ano, exportovat soukromý klíč**. Počítač, do kterého se klíč importuje, potřebuje k přečtení šifrovaných zpráv privátní klíč.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png" alt-text="Stránka export privátního klíče Průvodce exportem certifikátu" border="true":::

1. Na stránce **Formát souboru pro export** ponechte výchozí nastavení a pak vyberte **Další**.
1. Na stránce **heslo** zadejte heslo pro privátní klíč. V případě **šifrování** vyberte možnost **TripleDES-SHA1**. Pak vyberte **Další**.
1. Na stránce **soubor pro export** zadejte cestu a název exportovaného souboru certifikátu a pak vyberte **Další**. Název souboru musí mít příponu. pfx. Tento soubor je nakonfigurovaný v Azure Portal, aby se navázalo zabezpečené připojení.
1. Povoluje protokol LDAPs pro spravovanou doménu Azure služba AD DS.
   1. Z Azure Portal vyberte doménu `HDIFabrikam.com` .
   1. V části **Spravovat** vyberte **protokol Secure LDAP**.
   1. Na stránce **protokol Secure LDAP** v části **protokol Secure LDAP** vyberte **Povolit**.
   1. Vyhledejte soubor certifikátu. pfx, který jste exportovali v počítači.
   1. Zadejte heslo certifikátu.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png" alt-text="Povolit zabezpečený protokol LDAP" border="true":::

1. Teď, když jste povolili LDAPs, se ujistěte, že je dosažitelná povolením portu 636.
   1. Ve skupině prostředků **HDIFabrikam-CentralUS** vyberte skupinu zabezpečení sítě **AADDS-HDIFabrikam.com-NSG**.
   1. V části **Nastavení** vyberte **příchozí pravidla zabezpečení**  >  **Přidat**.
   1. Na stránce **Přidat pravidlo zabezpečení příchozího připojení** zadejte následující vlastnosti a vyberte **Přidat**:

      | Vlastnost | Hodnota |
      |---|---|
      | Zdroj | Libovolný |
      | Rozsahy zdrojových portů | * |
      | Cíl | Libovolný |
      | Rozsah cílových portů | 636 |
      | Protokol | Libovolný |
      | Akce | Povolit |
      | Priorita | \<Desired number> |
      | Název | Port_LDAP_636 |

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png" alt-text="Dialogové okno Přidat příchozí pravidlo zabezpečení" border="true":::

**HDIFabrikamManagedIdentity** je spravovaná identita přiřazená uživatelem. Role přispěvatele služby HDInsight Domain Services je povolená pro spravovanou identitu, která umožní této identitě číst, vytvářet, upravovat a odstraňovat operace služby Domain Services.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png" alt-text="Vytvoření spravované identity přiřazené uživatelem" border="true":::

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Vytvoření clusteru HDInsight s povoleným protokolem ESP

Tento krok vyžaduje následující požadavky:

1. Vytvořte novou skupinu prostředků *HDIFabrikam-WestUS* v umístění **západní USA**.
1. Vytvořte virtuální síť, která bude hostovat cluster HDInsight s podporou protokolu ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Vytvořte partnerský vztah mezi virtuální sítí, která je hostitelem služby Azure služba AD DS ( `HDIFabrikam-AADDSVNET` ) a virtuální sítě, která bude hostovat cluster HDInsight s podporou protokolu ESP ( `HDIFabrikam-HDIVNet` ). K navázání partnerského vztahu mezi dvěma virtuálními sítěmi použijte následující kód PowerShellu.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Vytvořte nový účet Azure Data Lake Storage Gen2 s názvem **Hdigen2store**. Nakonfigurujte účet pomocí uživatelsky spravované identity **HDIFabrikamManagedIdentity**. Další informace najdete v tématu [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Nastavte vlastní DNS ve virtuální síti **HDIFabrikam-AADDSVNET** .
    1. Do **skupin prostředků** služby Azure Portal >  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **servery DNS**.
    1. Vyberte **vlastní** a zadejte *10.0.0.4* a *10.0.0.5*.
    1. Vyberte **Uložit**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png" alt-text="Uložení vlastních nastavení DNS pro virtuální síť" border="true":::

1. Vytvořte nový cluster HDInsight Spark s povoleným protokolem ESP.
    1. Vyberte **vlastní (velikost, nastavení, aplikace)**.
    1. Zadejte podrobnosti o **základech** (oddíl 1). Zajistěte, aby byl **cluster typu** **Spark 2,3 (HDI 3,6)**. Ujistěte se, že je **Skupina prostředků** **HDIFabrikam-CentralUS**.

    1. Pro **zabezpečení a sítě** (část 2) vyplňte následující podrobnosti:
        * V části **balíček zabezpečení podniku** vyberte **povoleno**.
        * Vyberte možnost **uživatel správce clusteru** a vyberte účet **HDIAdmin** , který jste vytvořili jako místní uživatel s oprávněními správce. Klikněte na **Vybrat**.
        * Vyberte **skupinu přístupu clusteru**  >  **HDIUserGroup**. Každý uživatel, který přidáte do této skupiny v budoucnosti, bude mít přístup k clusterům HDInsight.

            :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg" alt-text="Vyberte skupinu přístupu clusteru HDIUserGroup" border="true":::

    1. Proveďte další kroky konfigurace clusteru a ověřte podrobnosti o **souhrnu clusteru**. Vyberte **Vytvořit**.

1. Přihlaste se k uživatelskému rozhraní Ambari pro nově vytvořený cluster v `https://CLUSTERNAME.azurehdinsight.net` . Použijte uživatelské jméno správce `hdiadmin@hdifabrikam.com` a jeho heslo.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg" alt-text="Přihlašovací okno uživatelského rozhraní Apache Ambari" border="true":::

1. Z řídicího panelu clusteru vyberte **role**.
1. Na stránce **role** v části **přiřadit role k těmto** položkám vedle role **Správce clusteru** zadejte skupinu *hdiusergroup*.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg" alt-text="Přiřazení role Správce clusteru k hdiusergroup" border="true":::

1. Otevřete klienta Secure Shell (SSH) a přihlaste se ke clusteru. Použijte **hdiuser** , který jste vytvořili v místní instanci služby Active Directory.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg" alt-text="Přihlaste se ke clusteru pomocí klienta SSH." border="true":::

Pokud se k tomuto účtu můžete přihlásit, nakonfigurovali jste cluster ESP správně, aby se synchronizovala s místní instancí Active Directory.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Úvod k zabezpečení Apache Hadoop pomocí protokolu ESP](hdinsight-security-overview.md).
