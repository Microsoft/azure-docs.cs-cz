---
title: Vytvořit, nakonfigurovat clustery balíčků podnikového zabezpečení – Azure
description: Přečtěte si, jak vytvořit a nakonfigurovat clustery balíčků enterprise security v Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: fb3484d013314897ea2e9157b642d8f2b85dcd60
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437636"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Vytváření a konfigurace clusterů balíčků podnikového zabezpečení v Azure HDInsight

Balíček zabezpečení rozlehlé sítě (ESP) pro Azure HDInsight vám poskytuje přístup k ověřování na základě služby Active Directory, víceuživatelské podpoře a řízení přístupu na základě rolí pro clustery Apache Hadoop v Azure. Clustery HDInsight ESP umožňují organizacím, které dodržují přísné podnikové zásady zabezpečení, bezpečně zpracovávat citlivá data.

Tato příručka ukazuje, jak vytvořit cluster Azure HDInsight s podporou ESP. Ukazuje také, jak vytvořit virtuální počítač Se systémem Windows IaaS, na kterém jsou povoleny služby Active Directory a DNS. Pomocí této příručky můžete nakonfigurovat potřebné prostředky, které místním uživatelům umožní přihlásit se ke clusteru HDInsight s podporou protokolu ESP.

Server, který vytvoříte, bude sloužit jako náhrada za *skutečné* místní prostředí. Budete ji používat pro kroky nastavení a konfigurace. Později budete opakovat kroky ve svém vlastním prostředí.

Tato příručka vám také pomůže vytvořit prostředí hybridní identity pomocí synchronizace hash hesel s Azure Active Directory (Azure AD). Průvodce doplňuje [použití ESP v HDInsight](apache-domain-joined-architecture.md).

Před použitím tohoto procesu ve vlastním prostředí:

* Nastavte službu Active Directory a službu DNS.
* Povolte Azure AD.
* Synchronizace místních uživatelských účtů do Azure AD.

![Diagram architektury Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Vytvoření místního prostředí

V této části použijete šablonu nasazení Azure QuickStart k vytvoření nových virtuálních počítačů, konfiguraci DNS a přidání nové doménové struktury služby Active Directory.

1. Přejděte na šablonu nasazení na úvodním startu [a vytvořte virtuální počítač Azure s novou doménovou doménovou strukturu služby Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Vyberte **Nasazení do Azure**.
1. Přihlaste se ke svému předplatnému Azure.
1. Na stránce **Vytvořit virtuální počítač Azure s novou doménovou strukturu služby AD** zadejte následující informace:

    |Vlastnost | Hodnota |
    |---|---|
    |Předplatné|Vyberte předplatné, ve kterém chcete prostředky nasadit.|
    |Skupina prostředků|Vyberte **Vytvořit nový**a zadejte název.`OnPremADVRG`|
    |Umístění|Vyberte umístění.|
    |Uživatelské jméno správce|`HDIFabrikamAdmin`|
    |Heslo správce|Zadejte heslo.|
    |Název domény|`HDIFabrikam.com`|
    |Předpona DNS|`hdifabrikam`|

    Ponechte zbývající výchozí hodnoty.

    ![Šablona pro vytvoření virtuálního počítače Azure s novou doménovou strukturu Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Přečtěte si **smluvní podmínky**a pak vyberte Souhlasím s **výše uvedenými podmínkami**.
1. Vyberte **Možnost Nákup**a sledujte nasazení a počkejte na jeho dokončení. Nasazení trvá přibližně 30 minut.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurace uživatelů a skupin pro přístup ke clusteru

V této části vytvoříte uživatele, kteří budou mít přístup ke clusteru HDInsight do konce této příručky.

1. Připojte se k řadiči domény pomocí vzdálené plochy.
    1. Z portálu Azure přejděte na **skupiny** > prostředků**OnPremADVRG** > **adVM** > **Connect**.
    1. V rozevíracím seznamu **IP adres** vyberte veřejnou IP adresu.
    1. Vyberte **Stáhnout soubor RDP**a otevřete soubor.
    1. Použít `HDIFabrikam\HDIFabrikamAdmin` jako uživatelské jméno.
    1. Zadejte heslo, které jste zvolili pro účet správce.
    1. Vyberte **OK**.

1. Na řídicím panelu **Správce serveru** řadiče domény přejděte na **nástroj nástroje** > **nástroje AD Uživatelé a počítače služby Active Directory**.

    ![Na řídicím panelu Správce serveru otevřete službu Správa služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Vytvořte dva nové uživatele: **HDIAdmin** a **HDIUser**. Tito dva uživatelé se přihlásí ke clusterům HDInsight.

    1. Na stránce **Uživatelé a počítače služby Active Directory** klepněte pravým tlačítkem myši na `HDIFabrikam.com`**položku** **Nový** > uživatel .

        ![Vytvoření nového uživatele služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na stránce Nový objekt - `HDIUser` **uživatel** zadejte pro **jméno** a přihlašovací **jméno uživatele**. Ostatní pole se automaticky zaplní. Pak vyberte **Další**.

        ![Vytvoření prvního objektu uživatele správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. V okně, které se zobrazí, zadejte heslo pro nový účet. Vyberte **Heslo nikdy vyprší**a potom **OK** na pop-up zprávy.
    1. Vyberte **Další**a potom **dokončit** vytvořte nový účet.
    1. Chcete-li vytvořit uživatele, `HDIAdmin`opakujte výše uvedené kroky .

        ![Vytvoření druhého objektu uživatele správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Vytvořte globální skupinu zabezpečení.

    1. V **souboru Uživatelé a počítače služby Active Directory**klepněte pravým tlačítkem myši `HDIFabrikam.com`a přejděte na **položku Nová** > **skupina**.

    1. Zadejte `HDIUserGroup` do textového pole **Název skupiny.**

    1. Vyberte **OK**.

    ![Vytvoření nové skupiny služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Vytvoření nového objektu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Přidání členů do **skupiny HDIUserGroup**.

    1. Klepněte pravým tlačítkem myši na **příkaz HDIUser** a vyberte příkaz **Přidat do skupiny...**.
    1. Do pole **Zadejte názvy objektů, které chcete vybrat,** zadejte `HDIUserGroup`. Pak vyberte **OK**a **OK** znovu na pop-up.
    1. Opakujte předchozí kroky pro účet **HDIAdmin.**

        ![Přidání člena HDIUser do skupiny HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nyní jste vytvořili prostředí služby Active Directory. Přidali jste dva uživatele a skupinu uživatelů, kteří mají přístup ke clusteru HDInsight.

Uživatelé budou synchronizovány s Azure AD.

### <a name="create-an-azure-ad-directory"></a>Vytvoření adresáře Azure AD

1. Přihlaste se k portálu Azure.
1. Vyberte možnost Vytvořit `directory` **zdroj** a typ . Vyberte **možnost Vytvoření služby Azure active directory** > **Create**.
1. Do pole Název `HDIFabrikam` **organizace**zadejte .
1. V **položce Počáteční název domény**zadejte `HDIFabrikamoutlook`.
1. Vyberte **Vytvořit**.

    ![Vytvoření adresáře Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Vytvoření vlastní domény

1. V nové **službě Azure Active Directory**vyberte v části **Správa** **vlastní názvy domén**.
1. Vyberte **+ Přidat vlastní doménu**.
1. V části Vlastní `HDIFabrikam.com`název **domény**zadejte a vyberte Přidat **doménu**.
1. Potom [vyplňte: Přidejte informace DNS do doménového registrátora](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Vytvoření vlastní domény](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Vytvoření skupiny

1. V nové **službě Azure Active Directory**vyberte v části **Správa** **položku Skupiny**.
1. Vyberte **+ Nová skupina**.
1. Do textového pole název `AAD DC Administrators` **skupiny** zadejte .
1. Vyberte **Vytvořit**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurace klienta Azure AD

Teď nakonfigurujete klienta Azure AD tak, abyste mohli synchronizovat uživatele a skupiny z místní instance služby Active Directory do cloudu.

Vytvořte správce klienta služby Active Directory.

1. Přihlaste se na portál Azure a vyberte svého klienta Azure AD, **HDIFabrikam**.

1. Přejděte na **spravovat** > **uživatele** > **nový uživatel**.

1. Zadejte následující podrobnosti o novém uživateli:

    **Identita**

    |Vlastnost |Popis |
    |---|---|
    |Uživatelské jméno|Zadejte `fabrikamazureadmin` do textového pole. V rozevíracím seznamu Název domény vyberte`hdifabrikam.com`|
    |Name (Název)| Zadejte `fabrikamazureadmin`.|

    **Heslo**
    1. Vyberte **Možnost Ponechat si vytvořit heslo**.
    1. Zadejte bezpečné heslo podle vašeho výběru.

    **Skupiny a role**
    1. Vyberte **vybráno 0 vybraných skupin**.
    1. Vyberte **položku Správci řadiče domény služby AAD**a **potom vyberte položku**.

    ![Dialogové okno Skupiny Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Vyberte **možnost Uživatel**.
    1. Vyberte **globálního správce**a **vyberte**.

    ![Dialogové okno role Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Vyberte **Vytvořit**.

1. Pak se nový uživatel přihlásit na portál Azure, kde se zobrazí výzva ke změně hesla. Před konfigurací služby Microsoft Azure Active Directory Connect to budete muset udělat.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizace místních uživatelů s Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurace připojení služby Microsoft Azure Active Directory

1. Z řadiče domény si stáhněte [službu Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Otevřete stažený spustitelný soubor a odsouhlaste licenční podmínky. Vyberte **Pokračovat**.

1. Vyberte **Použít expresní nastavení**.

1. Na stránce **Připojit k Azure AD** zadejte uživatelské jméno a heslo globálního správce pro Azure AD. Použijte uživatelské `fabrikamazureadmin@hdifabrikam.com` jméno, které jste vytvořili při konfiguraci klienta služby Active Directory. Pak vyberte **Další**.

    ![Stránka "Připojit k Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na stránce **Připojit ke službě Active Directory Domain Services** zadejte uživatelské jméno a heslo pro účet správce rozlehlé sítě. Použijte uživatelské `HDIFabrikam\HDIFabrikamAdmin` jméno a jeho heslo, které jste vytvořili dříve. Pak vyberte **Další**.

   ![Stránka "Připojit k Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stránce **konfigurace přihlášení služby Azure AD** vyberte **Další**.
   ![Stránka "Konfigurace přihlášení služby Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na stránce **Připraveno ke konfiguraci** vyberte **Instalovat**.

   ![Stránka "Připraveno ke konfiguraci"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na stránce **Dokončení konfigurace** vyberte **Ukončit**.
   ![Stránka "Konfigurace dokončena"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po dokončení synchronizace zkontrolujte, že uživatelé, které jste vytvořili v adresáři IaaS, jsou synchronizováni do Služby Azure AD.
   1. Přihlaste se k portálu Azure.
   1. Vyberte**možnost Uživatelé** **služby Azure Active Directory** > **HDIFabrikam** > .

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte uživatelem přiřazenou spravovanou identitu, kterou můžete použít ke konfiguraci služby Azure AD Domain Services (Azure AD DS). Další informace najdete [v tématu Vytvoření, seznam, odstranění nebo přiřazení role spravované identitě přiřazené uživateli pomocí webu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Přihlaste se k portálu Azure.
1. Vyberte možnost Vytvořit `managed identity` **zdroj** a typ . Vyberte **vytvoření spravované identity přiřazené uživateli** > **Create**.
1. Pro **název zdroje** `HDIFabrikamManagedIdentity`zadejte .
1. Vyberte své předplatné.
1. V části **Skupina prostředků**vyberte **Vytvořit nový** a zadejte `HDIFabrikam-CentralUS`.
1. V části **Umístění**vyberte **Centrální USA**.
1. Vyberte **Vytvořit**.

![Vytvoření nové spravované identity přiřazené uživateli](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Povolení služby Azure AD DS

Následujícím postupem povolte Azure AD DS. Další informace najdete [v tématu Povolení Azure AD DS pomocí portálu Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Vytvořte virtuální síť pro hostování Azure AD DS. Spusťte následující kód Prostředí PowerShell.

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

1. Přihlaste se k portálu Azure.
1. Vyberte **Vytvořit** `Domain services`prostředek , zadejte a vyberte**Vytvořit** **službu Azure AD Domain Services** > .
1. Na stránce **Základy:**
    1. V **části Název adresáře**vyberte adresář Azure AD, který jste **vytvořili: HDIFabrikam**.
    1. Do **pole Název domény DNS**zadejte *HDIFabrikam.com*.
    1. Vyberte své předplatné.
    1. Zadejte skupinu prostředků **HDIFabrikam-CentralUS**. V **pouzdřite možnost Umístění**vyberte možnost Střední **USA**.

        ![Základní podrobnosti o azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na stránce **Síť** vyberte síť (**HDIFabrikam-VNET)** a podsíť (**AADDS-podsíť),** kterou jste vytvořili pomocí skriptu Prostředí PowerShell. Nebo zvolte **Vytvořit nový** pro vytvoření virtuální sítě teď.

    ![Krok Vytvoření virtuální sítě](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na stránce **skupiny Správce** by se mělo zobrazit oznámení, že pro správu této skupiny již byla vytvořena skupina s názvem **Správci řadiče domény AAD.** Členství v této skupině můžete upravit, pokud chcete, ale v tomto případě jej nemusíte měnit. Vyberte **OK**.

    ![Zobrazení skupiny správců Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na stránce **Synchronizace** povolte úplnou synchronizaci výběrem **možnosti Vše** > **v pořádku**.

    ![Povolení synchronizace Služby Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na stránce **Souhrn** ověřte podrobnosti pro Azure AD DS a vyberte **OK**.

    ![Souhrn "Povolit služby domény Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Po povolení Azure AD DS, místní DNS server běží na virtuálních počítačích Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurace virtuální sítě Azure AD DS

Pomocí následujících kroků nakonfigurujte virtuální síť Azure AD DS **(HDIFabrikam-AADDSVNET)** pro použití vlastních serverů DNS.

1. Vyhledejte adresy IP vlastních serverů DNS.
    1. Vyberte `HDIFabrikam.com` prostředek Azure AD DS.
    1. V části **Manage**vyberte **Vlastnosti**.
    1. Vyhledejte IP adresy v části **IP adresa ve virtuální síti**.

    ![Vyhledání vlastních IP adres DNS pro Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Nakonfigurujte **hdiFabrikam-AADDSVNET** tak, aby používala vlastní IP adresy 10.0.0.4 a 10.0.0.5.

    1. V části **Nastavení**vyberte **položku DNS Servers**.
    1. Vyberte **vlastní**.
    1. Do textového pole zadejte první ADRESU IP (*10.0.0.4*).
    1. Vyberte **Uložit**.
    1. Opakováním kroků přidejte další adresu IP (*10.0.0.5*).

V našem scénáři jsme nakonfigurovali Azure AD DS tak, aby používalip adresy 10.0.0.4 a 10.0.0.5, nastavení stejné IP adresy ve virtuální síti Azure AD DS:

![Stránka vlastních serverů DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpečení provozu LDAP

Protokol LDAP (Lightweight Directory Access Protocol) se používá ke čtení a zápisu do služby Azure Active Directory. Provoz LDAP můžete zajistit jako důvěrný a zabezpečený pomocí technologie SSL (Secure Sockets L) (SSL) nebo Transport Layer Security (TLS). Protokol LDAP lze povolit přes protokol LDAPS (SSL) instalací správně formátovaného certifikátu.

Další informace o zabezpečeném protokolu LDAP [najdete v tématu Konfigurace protokolu LDAPS pro spravovanou doménu Služby Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

V této části vytvoříte certifikát podepsaný svým držitelem, stáhnete certifikát a nakonfigurujete ldaps pro spravovanou doménu **HDIFabrikam** Azure AD DS.

Následující skript vytvoří certifikát pro **HDIFabrikam**. Certifikát je uložen v cestě *LocalMachine.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> K vytvoření žádosti o certifikát TLS/SSL \#lze použít libovolný nástroj nebo aplikaci, která vytvoří platnou žádost o kryptografické standardy veřejného klíče (PKCS) 10.

Ověřte, zda je certifikát nainstalován v **osobním** úložišti počítače:

1. Spusťte konzolu MMC (MMC).
1. Přidejte modul snap-in **Certifikáty,** který spravuje certifikáty v místním počítači.
1. Rozbalte **certifikáty (místní počítač)** > **osobní** > **certifikáty**. Nový certifikát by měl existovat v **osobním** úložišti. Tento certifikát je vydán na plně kvalifikovaný název hostitele.

    ![Ověření vytvoření místního certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. V podokně vpravo klikněte pravým tlačítkem myši na certifikát, který jste vytvořili. Přejděte na **všechny úkoly**a vyberte **exportovat**.

1. Na stránce **Exportovat soukromý klíč** vyberte **Ano, exportujte soukromý klíč**. Počítač, ve kterém bude klíč importován, potřebuje k čtení šifrovaných zpráv soukromý klíč.

    ![Stránka Exportovat soukromý klíč Průvodce exportem certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stránce **Exportovat formát souboru** ponechejte výchozí nastavení a pak vyberte **Další**.
1. Na stránce **Heslo** zadejte heslo pro soukromý klíč. V **případě šifrování**vyberte možnost **TripleDES-SHA1**. Pak vyberte **Další**.
1. Na stránce **Soubor k exportu** zadejte cestu a název exportovaného souboru certifikátu a pak vyberte **Další**. Název souboru musí mít příponu .pfx. Tento soubor je nakonfigurován na webu Azure Portal k navázání zabezpečeného připojení.
1. Povolte LDAPS pro spravovanou doménu Azure AD DS.
    1. Na webu Azure Portal `HDIFabrikam.com`vyberte doménu .
    1. V části **Manage**vyberte **Secure LDAP**.
    1. Na stránce **Secure LDAP** vyberte v části **Secure LDAP** **položku Enable**.
    1. Vyhledejte soubor certifikátu .pfx, který jste exportovali do počítače.
    1. Zadejte heslo certifikátu.

    ![Povolení zabezpečeného protokolu LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Teď, když jste povolili LDAPS, ujistěte se, že je dosažitelný povolením portu 636.
    1. Ve skupině prostředků **HDIFabrikam-CentralUS** vyberte skupinu zabezpečení sítě **AADDS-HDIFabrikam.com-NSG**.
    1. V části **Nastavení**vyberte **Možnost Přidat pravidla** > zabezpečení**Příchozí**.
    1. Na stránce **Přidat příchozí pravidlo zabezpečení** zadejte následující vlastnosti a vyberte **Přidat**:

        | Vlastnost | Hodnota |
        |---|---|
        | Zdroj | Všechny |
        | Rozsahy zdrojových portů | * |
        | Cíl | Všechny |
        | Rozsah cílových portů | 636 |
        | Protocol (Protokol) | Všechny |
        | Akce | Povolit |
        | Priorita | \<Požadované číslo> |
        | Name (Název) | Port_LDAP_636 |

    ![Dialogové okno Přidat příchozí pravidlo zabezpečení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** je spravovaná identita přiřazená uživatelem. Role přispěvatele doménových služeb HDInsight je povolena pro spravovanou identitu, která této identitě umožní číst, vytvářet, upravovat a odstraňovat operace doménových služeb.

![Vytvoření spravované identity přiřazené uživatelem](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Vytvoření clusteru HDInsight s podporou ESP

Tento krok vyžaduje následující požadavky:

1. Vytvořte novou skupinu prostředků *HDIFabrikam-WestUS* v umístění **– západní USA**.
1. Vytvořte virtuální síť, která bude hostitelem clusteru HDInsight s podporou ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Vytvořte partnerský vztah mezi virtuální sítí, která`HDIFabrikam-AADDSVNET`je hostitelem Azure AD DS ( )`HDIFabrikam-HDIVNet`a virtuální sítí, která bude hostovat cluster HDInsight s podporou ESP ( ). Použijte následující kód Prostředí PowerShell k vzájemnému připojení dvou virtuálních sítí.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Vytvořte nový účet Azure Data Lake Storage Gen2 s názvem **Hdigen2store**. Nakonfigurujte účet pomocí uživatelem spravované identity **HDIFabrikamManagedIdentity**. Další informace najdete [v tématu Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Nastavte vlastní DNS ve virtuální síti **HDIFabrikam-AADDSVNET.**
    1. Přejděte na portál Azure > **skupiny** > prostředků**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS servery**.
    1. Vyberte **vlastní** a zadejte *10.0.0.4* a *10.0.0.5*.
    1. Vyberte **Uložit**.

        ![Uložení vlastních nastavení DNS pro virtuální síť](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Vytvořte nový cluster HDInsight Spark s podporou ESP.
    1. Vyberte **Vlastní (velikost, nastavení, aplikace).**
    1. Zadejte podrobnosti o **základech** (oddíl 1). Ujistěte se, že **typ clusteru** je **Spark 2.3 (HDI 3.6)**. Ujistěte se, že **skupina prostředků** je **HDIFabrikam-CentralUS**.

    1. Pro **zabezpečení + sítě** (oddíl 2) vyplňte následující údaje:
        * V části **Balíček podnikového zabezpečení**vyberte **Možnost povoleno**.
        * Vyberte **správce clusteru a** vyberte účet **HDIAdmin,** který jste vytvořili jako místní správce. Klepněte na **tlačítko Vybrat**.
        * Vyberte **možnost Skupina pro přístup ke** > clusteru**HDIUserGroup**. Každý uživatel, který přidáte do této skupiny v budoucnu bude mít přístup k clustery HDInsight.

            ![Výběr skupiny přístupu ke clusteru HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Proveďte další kroky konfigurace clusteru a ověřte podrobnosti souhrnu **clusteru**. Vyberte **Vytvořit**.

1. Přihlaste se k uj.a. ambari pro nově vytvořený cluster na adrese `https://CLUSTERNAME.azurehdinsight.net`. Použijte své uživatelské `hdiadmin@hdifabrikam.com` jméno správce a jeho heslo.

    ![Okno pro přihlášení k unovém portálu Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Na řídicím panelu clusteru vyberte **Role**.
1. Na stránce **Role** v části **Přiřadit k těmto rolím**zadejte vedle role **Správce clusteru** skupinu *hdiusergroup*. 

    ![Přiřazení role správce clusteru společnosti HDIusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otevřete klienta zabezpečeného prostředí (SSH) a přihlaste se ke clusteru. Použijte **hdiuser,** který jste vytvořili v místní instanci služby Active Directory.

    ![Přihlášení ke clusteru pomocí klienta SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Pokud se pomocí tohoto účtu můžete přihlásit, nakonfigurovali jste cluster ESP správně tak, aby se synchronizoval s místní instancí služby Active Directory.

## <a name="next-steps"></a>Další kroky

Přečtěte si [úvod do zabezpečení Apache Hadoop s ESP](hdinsight-security-overview.md).
