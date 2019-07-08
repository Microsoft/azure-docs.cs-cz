---
title: Vytvoření a konfigurace Enterprise Security Package clustery v Azure HDInsight
description: Zjistěte, jak vytvořit a konfigurovat Enterprise Security Package clustery v Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: e9cb9a902cf60fbd3b297a72a7dfa836ee18c835
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484588"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Vytvoření a konfigurace Enterprise Security Package clustery v Azure HDInsight

Enterprise Security Package pro Azure HDInsight poskytuje přístup k ověřování na základě služby Active Directory, podpora více uživatelů a řízení přístupu na základě rolí pro své clustery Apache Hadoop v Azure. Clustery HDInsight ESP umožňují organizacím, které dodržují přísné firemním zásadám zabezpečení, bezpečně zpracovávat citlivá data.

Cílem tohoto průvodce je správně nakonfigurovat potřebné prostředky tak, aby místní uživatelé mohou přihlásit k ESP povolené clusteru HDInsight. Tento článek vás provede kroky potřebné k vytvoření clusteru Azure HDInsight Enterprise Security Package povolena. Postup se týká vytvoření virtuálního počítače IaaS s Windows pomocí služby Active Directory a služby DNS (Domain Name) povolena. Tento server bude fungovat jako náhrada za vaše **skutečné** v místním prostředí a umožní vám pokračujte kroky instalace a konfigurace, takže je můžete opakovat později ve vašem prostředí. Tato příručka vám také pomůže vytvořit prostředí hybridní identity pomocí synchronizace hodnot hash hesel se službou Azure Active Directory.

Tato příručka je určena k doplnění [použití Enterprise Security Package v HDInsight](apache-domain-joined-architecture.md)

Před použitím tohoto procesu ve vašem prostředí, instalace Active Directory a služby DNS (Domain Name). Také povolte Azure Active Directory a synchronizace s místními uživatelské účty do služby Azure Active Directory.

![Diagram architektury](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Vytvoření místního prostředí

Přehled: V této části použijete šablonu pro rychlé nasazení Azure k vytvoření nové virtuální počítače, konfigurace služby DNS (Domain Name) a novou doménovou strukturu AD.

1. Přejděte na [vytvoření virtuálního počítače Azure s novou doménovou strukturu AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), chcete-li zobrazit šablonu pro rychlé nasazení.

1. Klikněte na **nasazení do Azure**.
1. Přihlaste se ke svému předplatnému Azure.
1. Na **vytvoření virtuálního počítače Azure s novou doménovou strukturu AD** obrazovky, proveďte následující kroky:
    1. Vyberte předplatné, ve kterém chcete prostředky nasazené z **předplatné** rozevíracího seznamu.
    1. Vyberte **vytvořit nový** vedle **skupiny prostředků** a zadejte název **OnPremADVRG**
    1. Pro zbytek šablony polí zadejte následující údaje:

        * **Umístění**: USA – střed
        * **Uživatelské jméno správce**: HDIFabrikamAdmin
        * **Heslo správce**: < YOUR_PASSWORD >
        * **Domény**: HDIFabrikam.com
        * **Předpona DNS**: hdifabrikam

        ![Vytvoření šablony virtuálního počítače Azure a doménovou strukturou služby AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Klikněte na tlačítko **nákupu**
    1. Monitorujte nasazení a počkejte na její dokončení.
    1. Potvrzení se prostředky vytvoří ve skupině prostředků správné `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurace uživatelů a skupin pro přístup ke clusteru

Přehled: V této části vytvoříte uživatele, kteří budou mít přístup ke clusteru HDInsight na konci tohoto průvodce.

1. Připojte se k řadiči domény s použitím vzdálené plochy.
    1. Pokud jste použili šablony funkce uvedené na začátku, řadič domény je virtuální počítač volá **adVM** v `OnPremADVRG` skupinu prostředků.
    1. Přejděte na web Azure Portal > **skupiny prostředků** > **OnPremADVRG** > **adVM** > **připojit**.
    1. Klikněte na tlačítko **RDP** kartu a potom klikněte na tlačítko **stáhnout soubor RDP**.
    1. Uložte soubor do počítače a otevřete jej.
    1. Po zobrazení výzvy k zadání pověření, použijte `HDIFabrikam\HDIFabrikamAdmin` jako uživatelské jméno a pak zadejte heslo jako pro účet správce.

1. Jakmile se otevře relaci vzdálené plochy na virtuálního počítače řadiče domény, spusťte **Active Directory Users and Computers** z **správce serveru** řídicího panelu. Klikněte na tlačítko **nástroje** v pravém horním rohu a pak **Active Directory Users and Computers** z rozevíracího seznamu.

    ![Správce serveru otevřete Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Vytvoření dvou nových uživatelů, kteří **HDIAdmin**, **HDIUser**. Tyto dva uživatele se použije k přihlášení do clusterů HDInsight.

    1. V **Active Directory Users and Computers** obrazovce, klikněte na tlačítko **akce** > **nový** > **uživatele**.

        ![Vytvoření nového uživatele Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. V **nový objekt - uživatel** obrazovky, zadejte `HDIUser` jako **přihlašovací uživatelské jméno** a klikněte na tlačítko **Další**.

        ![Vytvoření prvního uživatele s rolí správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. V místní nabídce, která se zobrazí zadejte požadované heslo pro nový účet. Zaškrtněte políčko s textem **platnost hesla nikdy nevyprší**. HDIClick **OK**.
    1. Klikněte na tlačítko **Dokončit** vytvořte nový účet.
    1. Vytvořit jiný uživatel `HDIAdmin`.

        ![Vytvořte druhý uživatel s rolí správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. V **Active Directory Users and Computers** obrazovce, klikněte na tlačítko **akce** > **nový** > **skupiny**. Vytvoření `HDIUserGroup` jako novou skupinu.

    ![Vytvoření nové skupiny služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Vytvořit nový skupina2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Přidat **HDIUser** vytvořili v předchozím kroku, a **HDIUserGroup** jako člena.

    1. Klikněte pravým tlačítkem na **HDIUserGroup** a klikněte na tlačítko **vlastnosti**.
    1. Přejděte na **členy** kartě a klikněte na tlačítko **přidat**.
    1. Zadejte `HDIUser` v poli označeném **zadejte názvy objektů k výběru** a klikněte na tlačítko **OK**.
    1. Opakujte předchozí kroky pro jiný účet `HDIAdmin`

        ![Přidat členy do skupiny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nyní jste vytvořili prostředí služby Active Directory společně s dva uživatelé a skupiny uživatelů pro přístup ke clusteru HDInsight.

Tito uživatelé se budou synchronizovat s Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Vytvoření nové Azure Active Directory

1. Přihlaste se k portálu Azure.
1. Klikněte na tlačítko **vytvořit prostředek** a typ **directory**. Vyberte **Azure Active Directory** > **vytvořit**.
1. Zadejte **HDIFabrikam** pod **název organizace**.
1. Zadejte **HDIFabrikamoutlook** pod **počáteční název domény**.
1. Klikněte na možnost **Vytvořit**.
1. Na levé straně webu Azure Portal, klikněte na tlačítko **Azure Active Directory**.
1. V případě potřeby klikněte na tlačítko **přepnout adresář** přejdete do nového adresáře, který jste vytvořili **HDIFabrikamoutlook**.
1. V části **spravovat** klikněte na tlačítko **vlastní názvy domén** > **přidat vlastní doménu**.
1. Zadejte **HDIFabrikam.com** pod **vlastní název domény** a klikněte na tlačítko **přidáním domény**.

![Vytvoření nové azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Vytvořit novou vlastní doménu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurace vašeho tenanta Azure AD

Přehled: Teď nakonfigurujete vašeho tenanta Azure AD tak, aby můžete synchronizovat uživatele a skupiny z místní AD v cloudu.

1. Vytvořte správce klienta AD.
    1. Přihlaste se k webu Azure portal a vyberte tenanta Azure AD **HDIFabrikam**
    1. Vyberte **uživatelé** pod **spravovat** a potom **nového uživatele**.
    1. Zadejte následující podrobnosti pro nového uživatele:

        * Název: fabrikamazureadmin
        * Uživatelské jméno: fabrikamazureadmin@hdifabrikam.com
        * Heslo: zabezpečené heslo podle vašeho výběru

    1. Klikněte na **skupiny** části, vyhledejte **správci AAD DC**a klikněte na tlačítko **vyberte**.

        ![Skupiny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Klikněte na **role adresáře** a vyberte **globálního správce** na pravé straně. Klikněte na tlačítko **OK**.

        ![Role adresáře](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Zadejte heslo pro daného uživatele. Klikněte na možnost **Vytvořit**.

1. Pokud chcete změnit heslo pro nově vytvořený uživatel <fabrikamazureadmin@hdifabrikam.com>. Přihlaste se k webu Azure portal pomocí, identity a pak se zobrazí výzva ke změně hesla.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizace místních uživatelů do služby Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Stáhněte a nainstalujte Microsoft Azure Active Directory connect

1. [Stáhnout Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Instalace Microsoft Azure Active Directory připojit k řadiči domény.
    1. Spusťte spustitelný soubor, který jste stáhli v předchozím kroku a souhlas s licenčními podmínkami. Klikněte na **Pokračovat**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Klikněte na tlačítko **použít Expresní nastavení** a dokončete instalaci.

        ![Použít Expresní nastavení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurovat synchronizaci s místním řadičem domény

1. Na **připojit ke službě Azure AD** obrazovky, zadejte uživatelské jméno a heslo globálního správce pro službu Azure AD. Klikněte na tlačítko **Další**. Toto je uživatelské jméno `fabrikamazureadmin@hdifabrikam.com` , který jste vytvořili při konfiguraci svého tenanta služby AD.
    ![Připojení k Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Na **připojit k Active Directory Domain Services** obrazovky, zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Klikněte na tlačítko **Další**. Toto je uživatelské jméno `HDIFabrikam\HDIFabrikamAdmin` a odpovídající heslo, které jste vytvořili dříve.

   ![Připojte se k Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Na **konfigurace přihlášení k Azure AD** klikněte na **Další**.
    ![Konfigurace Azure AD přihlášení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Na obrazovce připraveno ke konfiguraci, klikněte na tlačítko **nainstalovat**.
    ![Instalace](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Když **konfiguraci Dokončit** zobrazenou obrazovku na jinou, klikněte na tlačítko **ukončovací**.
    ![Dokončení konfigurace](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Po dokončení synchronizace potvrďte, pokud uživatelé, které jste vytvořili ve službě Active Directory IAAS se synchronizují do služby Azure Active Directory.
    1. Přihlaste se k portálu Azure.
    1. Vyberte **Azure Active Directory** > **HDIFabrikam** > **uživatelé**.

### <a name="create-an-user-assigned-managed-identity"></a>Vytvoření uživatelsky přiřazené identity spravované

Vytvoření uživatelsky přiřazené spravovaná identita, která se použije ke konfiguraci Azure Active Directory Domain Services (Azure AD DS). Další informace o vytvoření uživatelsky přiřazené spravovanou identitu, naleznete v tématu [vytvoření, list, delete nebo přiřadit roli, kterou chcete přiřadit uživatele spravovanou identitu pomocí webu Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Přihlaste se k portálu Azure.
1. Klikněte na tlačítko **vytvořit prostředek** a typ **se identita spravované**. Vyberte **spravované Identity přiřazené uživateli** > **vytvořit**.
1. Zadejte **HDIFabrikamManagedIdentity** jako **název prostředku**.
1. Vyberte své předplatné.
1. V části **skupiny prostředků** klikněte na tlačítko **vytvořit nový** a zadejte **HDIFabrikam CentralUS**.
1. Vyberte **USA (střed)** pod **umístění**.
1. Klikněte na možnost **Vytvořit**.

![vytvořit nové spravované identity přiřazené uživateli](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Povolení služby Azure Active Directory Domain Services

Další informace najdete v tématu [povolit Azure Active Directory Domain Services pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Vytvoření virtuální sítě pro hostitele služby Azure AD – DS. Spusťte následující kód powershellu.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Přihlaste se k portálu Azure.
1. Klikněte na tlačítko **vytvořit prostředek**, zadejte **Domain services** a vyberte **Azure AD Domain Services**.
1. Na **Základy** obrazovky proveďte následující kroky:
    1. V části **název adresáře** vyberte Azure Active Directory pro účely tohoto článku vytvořili **HDIFabrikam**.
    1. Zadejte **název domény DNS** z **HDIFabrikam.com**.
    1. Vyberte své předplatné.
    1. Zadejte skupinu prostředků **HDIFabrikam CentralUS** a **umístění** z **USA (střed)** .

        ![Podrobnosti základní služby Azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Na **sítě** obrazovky kompletní, vyberte síť (**HDIFabrikam-VNET**) a v podsíti (**AADDS podsítě**), kterou jste vytvořili pomocí předchozího skriptu prostředí powershell. Nebo můžete použít **vytvořit nový** možnost nyní vytvořit virtuální síť.

    ![Vyberte síť](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Na **skupiny správců** obrazovky, zobrazí se oznámení, že se vytvoří skupina s názvem **správci AAD DC** již byla vytvořená za účelem správy této skupiny. Volitelně můžete upravit členství této skupiny, ale není nutné k provedení kroků v tomto článku. Klikněte na **OK**.

    ![zobrazení správce skupiny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Na **synchronizace** obrazovky, povolit úplnou synchronizaci tak, že vyberete **všechny** a potom klikněte na tlačítko **OK**.

    ![Povolit synchronizaci](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Na **Souhrn** obrazovky, ověřte podrobnosti pro Azure AD – DS a klikněte na tlačítko **Ok**.

    ![Ověřte podrobnosti](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Po povolení služby Azure AD-DS místní server služby DNS (Domain Name) běží na virtuálních počítačích (VM) AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurace virtuální sítě Azure AD-DS

Kroky v této části vám pomohou konfigurovat vaše virtuální síť Azure AD – DS (**HDIFabrikam AADDSVNET**) použít vlastní servery DNS.

1. Vyhledejte vlastní servery DNS IP adresy. Klikněte na **HDIFabrikam.com** prostředků služby AD DS, klikněte na tlačítko **vlastnosti** pod **spravovat**   a podívejte se na IP adresy uvedené v části **IP Adresa ve virtuální síti**.

    ![Vyhledejte vlastní IP adresy serverů DNS pro Azure AD – DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurace **HDIFabrikam AADDSVNET** na vlastní IP adresy `10.0.0.4` a `10.0.0.5`.

    1. Vyberte **servery DNS** pod **nastavení** kategorie. Klikněte na přepínač vedle **vlastní**, zadejte do textového pole níže první IP adresa (10.0.0.4) a klikněte na tlačítko **Uložit**.
    1. Přidejte další IP adresy (10.0.0.5) pomocí stejného postupu.

1. V tomto scénáři Azure AD – DS nenakonfigurovali použití adres IP adresou 10.0.0.4 a adresu 10.0.0.5 stejné nastavení IP adresy ve virtuální síti AADDS jak je vidět na následujícím obrázku.

    ![Zobrazit vlastní dns servery](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Zabezpečení přenosu pomocí protokolu LDAP

Zjednodušené LDAP Directory Access Protocol () se používá k čtení a zápis do služby Active Directory. Provoz protokolu LDAP můžete provést utajení a zabezpečení pomocí vrstvy SSL (Secure Sockets) / technologie zabezpečení TLS (Transport Layer). Můžete povolit LDAP přes protokol SSL (LDAPS) po instalaci správně formátovaná certifikátu.

Další informace o protokolu secure LDAP najdete v tématu [konfigurace zabezpečeného protokolu LDAP (LDAPS) pro Azure AD Domain Services spravované domény](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

V této části vytvoříte certifikát podepsaný svým držitelem, stáhněte si certifikát a konfigurace zabezpečeného protokolu LDAP (LDAPS) pro **hdifabrikam** služby Azure AD – DS spravované domény.

Tento skript vytvoří certifikát pro hdifabrikam. Certifikát je uložen v cestě "LocalMachine".

> [!Note] 
> Libovolný nástroj nebo aplikaci, která vytváří platný PKCS \#10 požadavku můžete použít k vytvoření žádosti o certifikát SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Ověřte, že je tento certifikát nainstalován v počítači\'s osobním úložišti. Proveďte následující kroky:

1. Spusťte konzolu Microsoft Management Console (MMC).
1. Přidáte modul snap-in Certifikáty spravující certifikáty v místním počítači.
1. Rozbalte **certifikáty (místní počítač)** , rozbalte **osobní**a potom rozbalte **certifikáty**. Nový certifikát musí existovat v osobním úložišti. Tento certifikát je vydaný na plně kvalifikovaný název.

    ![Ověřte vytvoření certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. V pravém podokně klikněte pravým tlačítkem na certifikát, který jste vytvořili v předchozím kroku, přejděte na **všechny úkoly**a potom klikněte na tlačítko **exportovat**.

1. Na **exportovat soukromý klíč** klikněte na **Ano, exportovat privátní klíč**. Privátní klíč se požaduje pro šifrovaných zpráv ke čtení z počítače, do kterého se naimportuje klíč.

    ![exportovat privátní klíč](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Na **formát souboru pro Export** stránce, ponechejte výchozí nastavení a potom klikněte na tlačítko **Další**. 
1. Na **heslo** stránky, zadejte heslo pro privátní klíč, vyberte **TripleDES SHA1** pro **šifrování** a klikněte na tlačítko **Další**.
1. Na **soubor pro Export** stránky, zadejte cestu a název pro exportovaný soubor certifikátu a pak klikněte na tlačítko **Další**.
1. Název souboru musí být .pfx příponu, tento soubor je nakonfigurován na webu Azure portal vytvořit zabezpečené připojení.
1. Povolení zabezpečeného protokolu LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services.
    1. Vyberte doménu **HDIFabrikam.com** z portálu Azure portal.
    1. Klikněte na tlačítko **protokol Secure LDAP** pod **spravovat**.
    1. Na **protokolu Secure LDAP** obrazovce, klikněte na tlačítko **povolit** pod **protokolu Secure LDAP**.
    1. Projděte pro soubor certifikátu .pfx, který jste exportovali v počítači.
    1. Zadejte heslo certifikátu.

    ![Povolení protokolu LDAPS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Teď, když jste povolili zabezpečený LDAP, ujistěte se, že se že jedná o povolení portu 636 dostupný.
    1. Klikněte na skupinu zabezpečení sítě **AADDS-HDIFabrikam.com-NSG** v **HDIFabrikam CentralUS** skupinu prostředků.
    1. V části **nastavení** klikněte na tlačítko **příchozí pravidla zabezpečení** > **přidat**.
    1. Na **přidat příchozí pravidlo zabezpečení** obrazovky, zadejte následující vlastnosti a klikněte na tlačítko **přidat**:

        | Vlastnost | Hodnota |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number> |
        | Name | Port_LDAP_636 |

    ![Příchozí pravidlo zabezpečení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` je uživatel přiřazenou se identita spravované, roli přispěvatele služby HDInsight domény je povoleno pro spravovanou identitu, která vám umožní tato identita číst, vytvářet, upravovat a odstraňovat operacích v doméně služby.

    ![Vytvoření spravované identity přiřazené uživateli](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Vytváření Enterprise Security Package povolené clusteru HDInsight

Tento krok vyžaduje následující předpoklady:

1. Vytvořit novou skupinu prostředků `HDIFabrikam-WestUS` v umístění `West US`.
1. Vytvoření virtuální sítě, který bude hostitelem ESP povolené clusteru HDInsight.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Vytvoření vztahu sdílené mezi virtuální sítí, který je hostitelem AADDS (`HDIFabrikam-AADDSVNET`) a virtuální síť, která bude hostovat ESP povolené clusteru HDInsight (`HDIFabrikam-HDIVNet`). Pomocí následujícího kódu powershellu k navázání partnerského vztahu mezi těmito dvěma virtuálními sítěmi.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Vytvořit nový účet Azure Data Lake Storage Gen2 **Hdigen2store**, který má nakonfigurovanou identitu spravované uživatelem **HDIFabrikamManagedIdentity**. Další informace o vytváření účtů Data Lake Storage Gen2 povolené s uživatelem spravované identity, najdete v části [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Nastavit vlastní DNS na **HDIFabrikam AADDSVNET** virtuální sítě.
    1. Přejděte na web Azure Portal > **skupiny prostředků** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **Servery DNS**.
    1. Vyberte **vlastní** a zadejte `10.0.0.4` a `10.0.0.5`.
    1. Klikněte na **Uložit**.

        ![Uložit nastavení vlastní dns](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Vytvořte nový cluster s podporou ESP HDInsight Spark.
    1. Klikněte na tlačítko **vlastní (velikost, nastavení, aplikace)** .
    2. Zadejte požadované podrobnosti sekce 1 **Základy**. Ujistěte se, že **typ clusteru** je **2.3 Spark (HDI 3.6)** a **skupiny prostředků** je **HDIFabrikam CentralUS**

    1. V části 2 **zabezpečení a sítě**, proveďte následující kroky:
        1. Klikněte na tlačítko **povolené** pod **Enterprise Security Package**.
        1. Klikněte na tlačítko **uživatele s rolí Správce clusteru** a vyberte **HDIAdmin** účet, který jste vytvořili dříve jako uživatel s rolí správce v místním prostředí. Klikněte na **Vybrat**.

        1. Klikněte na tlačítko **clusteru přístupovou skupinu** a pak vyberte **HDIUserGroup**. Každý uživatel, který se v budoucnu přidat do této skupiny budou mít přístup k clustery HDInsight.

            ![Vyberte skupiny clusteru](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Proveďte další kroky konfigurace clusteru a ověřte podrobnosti na **souhrn clusteru**. Klikněte na možnost **Vytvořit**.

1. Přihlaste se na uživatelské rozhraní Ambari pro nově vytvořený cluster v `https://CLUSTERNAME.azurehdinsight.net` pomocí uživatelské jméno správce `hdiadmin@hdifabrikam.com` a heslo.

    ![Přihlaste se k Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Klikněte na tlačítko **role** z řídicí panel clusteru.
1. Na **role** stránky, zadejte skupinu **hdiusergroup** ji přiřadíte **Správce clusteru** role v rámci **přiřazení rolí k těmto**.

    ![přiřazení role Správce clusteru hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Otevřete klienta SSH a přihlaste se ke clusteru pomocí **hdiuser** , kterou jste vytvořili dříve v místní Active Directory.

    ![Přihlaste se ke clusteru pomocí SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Pokud budete se moct přihlásit s tímto účtem, pak jste nakonfigurovali ESP clusteru správně synchronizovat s místní active directory.

## <a name="next-steps"></a>Další postup

* [Úvod do Apache Hadoop zabezpečení s balíčkem Enterprise Security Package](apache-domain-joined-introduction.md)
