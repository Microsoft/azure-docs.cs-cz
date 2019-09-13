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
ms.openlocfilehash: 24c00d8d5db7c36746d68ad10edc4db4f76e0aac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918740"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Vytvoření a konfigurace clusterů Balíček zabezpečení podniku ve službě Azure HDInsight

Balíček zabezpečení podniku pro Azure HDInsight poskytuje přístup k ověřování založenému na službě Active Directory, podpoře více uživatelů a řízení přístupu na základě rolí pro vaše clustery Apache Hadoop v Azure. Clustery HDInsight ESP umožňují organizacím, které vyhovují přísným podnikovým zásadám zabezpečení pro bezpečné zpracování citlivých dat.

Cílem tohoto průvodce je správně nakonfigurovat nezbytné prostředky, aby se místní uživatelé mohli přihlásit ke clusteru HDInsight s povoleným protokolem ESP. Tento článek vás provede kroky potřebnými k vytvoření clusteru Azure HDInsight s povoleným Balíček zabezpečení podniku. Postup se zabývá vytvořením virtuálního počítače s Windows IaaS se zapnutou službou Active Directory & Domain Services (DNS). Tento server bude fungovat jako náhrada za vaše **skutečné** místní prostředí a umožní vám postupovat podle kroků instalace a konfigurace, abyste je mohli opakovat později ve svém vlastním prostředí. Tento průvodce vám také pomůže vytvořit hybridní prostředí identity pomocí synchronizace hodnot hash hesel pomocí Azure Active Directory.

Tato příručka je určena k doplnění [použití balíček zabezpečení podniku ve službě HDInsight](apache-domain-joined-architecture.md)

Před použitím tohoto procesu ve vlastním prostředí nastavte službu Active Directory a službu DNS (Domain Name Services). Povolte taky Azure Active Directory a synchronizaci místních uživatelských účtů pro Azure Active Directory.

![Diagram architektury](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-on-premises-environment"></a>Vytvoření místního prostředí

Přehled: V této části použijete šablonu rychlého nasazení Azure k vytvoření nových virtuálních počítačů, konfiguraci služby DNS (Domain Name Services) a nové doménové struktury služby AD.

1. Pokud chcete zobrazit šablonu rychlého nasazení, přejdete na [vytvořit virtuální počítač Azure s novou doménovou strukturou služby AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Klikněte na **nasadit do Azure**.
1. Přihlaste se ke svému předplatnému Azure.
1. Na obrazovce **vytvořit virtuální počítač Azure s novou doménovou strukturou služby AD** proveďte následující kroky:
    1. Vyberte předplatné, ve kterém chcete prostředky nasazené v rozevíracím seznamu **odběr** .
    1. V poli **Skupina prostředků** vyberte **vytvořit novou** a zadejte název **OnPremADVRG** .
    1. Pro zbytek polí šablony zadejte následující podrobnosti:

        * **Umístění**: Střed USA
        * **Uživatelské jméno správce**: HDIFabrikamAdmin
        * **Heslo správce**: < YOUR_PASSWORD >
        * **Doména**: HDIFabrikam.com
        * **Předpona DNS**: hdifabrikam

        ![Šablona – vytvoření virtuálního počítače Azure a doménové struktury AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Kliknout na **koupit**
    1. Monitorujte nasazení a počkejte, než se dokončí.
    1. Potvrďte, že se prostředky vytvoří pod správnou skupinou `OnPremADVRG`prostředků.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurace uživatelů a skupin pro přístup k clusteru

Přehled: V této části vytvoříte uživatele, kteří budou mít přístup ke clusteru HDInsight na konci této příručky.

1. Připojte se k řadiči domény pomocí vzdálené plochy.
    1. Pokud jste použili šablonu uvedenou na začátku, řadič domény je virtuálním počítačem s názvem **adVM** ve `OnPremADVRG` skupině prostředků.
    1. Do **skupin** > prostředků Azure Portal >**OnPremADVRG** > **Připojte** **adVM** > .
    1. Klikněte na kartu **RDP** a pak klikněte na **Stáhnout soubor RDP**.
    1. Uložte soubor do počítače a otevřete ho.
    1. Po zobrazení výzvy k zadání přihlašovacích údajů použijte `HDIFabrikam\HDIFabrikamAdmin` jako uživatelské jméno a pak zadejte heslo, které jste zvolili pro účet správce.

1. Po otevření relace vzdálené plochy na VIRTUÁLNÍm počítači řadiče domény spusťte modul **Uživatelé a počítače služby Active Directory** z řídicího panelu **Správce serveru** . V pravém horním rohu klikněte na **nástroje** a pak na **Uživatelé a počítače služby Active Directory** z rozevíracího seznamu.

    ![Správce serveru otevření správy služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Vytvořte dva nové uživatele, **HDIAdmin**, **HDIUser**. Tito dva uživatelé se budou používat k přihlašování ke clusterům HDInsight.

    1. Na obrazovce **Uživatelé a počítače služby Active Directory** klikněte na **Akce** > **Nový** > **uživatel**.

        ![Vytvořit nového uživatele služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na obrazovce **Nový objekt – uživatel** zadejte `HDIUser` **přihlašovací jméno uživatele** a klikněte na **Další**.

        ![Vytvořit prvního uživatele s oprávněními správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. V místní nabídce, která se zobrazí, zadejte požadované heslo pro nový účet. Zaškrtněte políčko, které říká, že **heslo je platné stále**. HDIClick **OK**.
    1. Kliknutím na tlačítko **Dokončit** vytvořte nový účet.
    1. Vytvořte jiného uživatele `HDIAdmin`.

        ![Vytvořit druhého uživatele správce](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Na obrazovce **Uživatelé a počítače služby Active Directory** klikněte na **Akce** > **Nová** > **Skupina**. Vytvořit `HDIUserGroup` jako novou skupinu.

    ![Vytvořit novou skupinu služby Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![vytvořit nové skupina2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Přidejte **HDIUser** vytvořené v předchozím kroku do **HDIUserGroup** jako člena.

    1. Klikněte pravým tlačítkem na **HDIUserGroup** a klikněte na **vlastnosti**.
    1. Přejděte na kartu **Členové** a klikněte na **Přidat**.
    1. Do pole s popiskem zadejte **názvy objektů k výběru** a klikněte na **OK.** `HDIUser`
    1. Opakujte předchozí postup pro druhý účet.`HDIAdmin`

        ![přidat členy do skupiny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Nyní jste vytvořili prostředí Active Directory společně se dvěma uživateli a skupinou uživatelů pro přístup ke clusteru HDInsight.

Tito uživatelé budou synchronizováni se službou Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Vytvořit nový Azure Active Directory

1. Přihlaste se k portálu Azure.
1. Klikněte na **vytvořit prostředek** a zadejte **adresář**. Vyberte **Azure Active Directory** > **vytvořit**.
1. Do **pole název organizace**zadejte **HDIFabrikam** .
1. Jako **počáteční název domény**zadejte **HDIFabrikamoutlook** .
1. Klikněte na možnost **Vytvořit**.
1. Na levé straně Azure Portal klikněte na **Azure Active Directory**.
1. V případě potřeby klikněte na **Přepnout adresář** a přejděte do nového adresáře, který jste vytvořili v **HDIFabrikamoutlook**.
1. V části **Spravovat** klikněte na **vlastní názvy** > domén**Přidat vlastní doménu**.
1. Do **pole vlastní název domény** zadejte **HDIFabrikam.com** a klikněte na **Přidat doménu**.

![Vytvoření nové služby Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Vytvoření nové vlastní domény](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurace tenanta Azure AD

Přehled: Teď nakonfigurujete tenanta Azure AD tak, aby bylo možné synchronizovat uživatele a skupiny z místní služby AD do cloudu.

1. Vytvořte správce klienta služby AD.
    1. Přihlaste se k Azure Portal a vyberte svého tenanta Azure AD **HDIFabrikam**
    1. V části **Spravovat** a **Nový uživatel**vyberte **Uživatelé** .
    1. Pro nového uživatele zadejte následující podrobnosti:

        * Název: fabrikamazureadmin
        * Uživatelské jméno:fabrikamazureadmin@hdifabrikam.com
        * Heslo: zabezpečené heslo podle vašeho výběru

    1. Klikněte na oddíl **skupiny** , vyhledejte **správce AAD řadiče domény**a klikněte na **Vybrat**.

        ![Skupiny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Klikněte na část **role adresáře** a na pravé straně vyberte **globální správce** . Klikněte na tlačítko **OK**.

        ![Role adresáře](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Zadejte heslo pro uživatele. Klikněte na možnost **Vytvořit**.

1. Pokud chcete změnit heslo nově vytvořeného uživatele <fabrikamazureadmin@hdifabrikam.com>. Přihlaste se k Azure Portal pomocí identity a potom se zobrazí výzva ke změně hesla.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizace místních uživatelů s Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Stažení a instalace Microsoft Azure Active Directory připojit

1. [Stáhněte si Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Nainstalujte Microsoft Azure Active Directory připojit na řadiči domény.
    1. Otevřete spustitelný soubor, který jste stáhli v předchozím kroku, a vyjádřete souhlas s licenčními podmínkami. Klikněte na **Pokračovat**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Klikněte na **Použít expresní nastavení** a dokončete instalaci.

        ![Použít expresní nastavení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurace synchronizace s místním řadičem domény

1. Na obrazovce **připojení ke službě Azure AD** zadejte uživatelské jméno a heslo globálního správce služby Azure AD. Klikněte na tlačítko **Další**. Toto je uživatelské jméno `fabrikamazureadmin@hdifabrikam.com` , které jste vytvořili při konfiguraci tenanta služby AD.
    ![Připojení k Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. Na obrazovce **připojit k Active Directory Domain Services** zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Klikněte na tlačítko **Další**. Toto je uživatelské jméno `HDIFabrikam\HDIFabrikamAdmin` a odpovídající heslo, které jste vytvořili dříve.

   ![Připojení k Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stránce **Konfigurace přihlášení k Azure AD** klikněte na **Další**.
    ![Konfigurace přihlášení k Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. Na obrazovce připraveno ke konfiguraci klikněte na **nainstalovat**.
    ![Instalace](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)
1. Po zobrazení obrazovky **Konfigurace byla dokončena** klikněte na tlačítko **konec**.
    ![Konfigurace byla dokončena](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po dokončení synchronizace potvrďte, jestli se uživatelé, které jste vytvořili v IAAS Active Directory, synchronizují s Azure Active Directory.
    1. Přihlaste se k portálu Azure.
    1. Vyberte **Azure Active Directory** > **Uživatelé** **HDIFabrikam** > .

### <a name="create-an-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte spravovanou identitu přiřazenou uživatelem, která se použije ke konfiguraci Azure Active Directory Domain Services (Azure AD-DS). Další informace o vytvoření spravované identity přiřazené uživatelem najdete v tématu věnovaném [Vytvoření, výpisu, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Přihlaste se k portálu Azure.
1. Klikněte na **vytvořit prostředek** a zadejte **spravovanou identitu**. Vyberte možnost > **vytvořit** **spravovanou identitu přiřazenou uživatelem**.
1. Jako **název prostředku**zadejte **HDIFabrikamManagedIdentity** .
1. Vyberte své předplatné.
1. V části **Skupina prostředků** klikněte na **vytvořit novou** a zadejte **HDIFabrikam-CentralUS**.
1. V části **umístění**vyberte **střed USA** .
1. Klikněte na možnost **Vytvořit**.

![vytvořit novou spravovanou identitu přiřazenou uživatelem](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Povolení služby Azure Active Directory Domain Services

Další informace najdete v tématu [povolení Azure Active Directory Domain Services pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Vytvořte Virtual Network pro hostování Azure AD-DS. Spusťte následující kód PowerShellu.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Přihlaste se k portálu Azure.
1. Klikněte na **vytvořit prostředek**, zadejte **Domain services** a vyberte **Azure AD Domain Services**.
1. Na obrazovce **základy** proveďte následující kroky:
    1. V části **název adresáře** vyberte Azure Active Directory vytvořeného pro tento článek, **HDIFabrikam**.
    1. Zadejte **název domény DNS** **HDIFabrikam.com**.
    1. Vyberte své předplatné.
    1. Zadejte skupinu prostředků **HDIFabrikam-CentralUS** a **umístění** **střed USA**.

        ![Azure AD – základní podrobnosti o DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Po dokončení obrazovky **síť** vyberte síť (**HDIFabrikam-VNet**) a podsíť (**AADDS-Subnet**), kterou jste vytvořili pomocí předchozího skriptu PowerShellu. Nebo můžete použít možnost **vytvořit novou** pro vytvoření virtuální sítě nyní.

    ![Vybrat síť](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na obrazovce **skupiny správců** byste měli vidět oznámení, že se pro správu této skupiny už vytvořila skupina s názvem **Správci AAD DC** . Volitelně můžete upravit členství v této skupině, ale není to nutné pro kroky tohoto článku. Klikněte na **OK**.

    ![Zobrazit skupinu správců](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na obrazovce **synchronizace** povolte kompletní synchronizaci výběrem možnosti **vše** a potom klikněte na tlačítko **OK**.

    ![Povolit synchronizaci](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na obrazovce **Souhrn** zkontrolujte podrobnosti pro Azure AD-DS a klikněte na **OK**.

    ![ověřit podrobnosti](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Po povolení služby Azure AD-DS se na Virtual Machines AD (VM) spustí místní server DNS (Domain Name Service).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurace virtuální sítě Azure AD-DS

Kroky v této části vám pomůžou nakonfigurovat virtuální síť Azure AD-DS (**HDIFabrikam-AADDSVNET**), která bude používat vaše vlastní servery DNS.

1. Vyhledejte IP adresy vašich vlastních serverů DNS. Klikněte na prostředek **HDIFabrikam.com** AD-DS, klikněte na **vlastnosti** v části **Spravovat**   a podívejte se na IP adresy uvedené v části **IP adresa v Virtual Network**.

    ![Vyhledání vlastních IP adres DNS pro Azure AD – DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Nakonfigurujte **HDIFabrikam-AADDSVNET** na vlastní IP `10.0.0.4` adresy `10.0.0.5`a.

    1. V kategorii  **Nastavení**Vyberteservery DNS. pak klikněte na přepínač vedle **vlastní**, do textového pole níže zadejte první IP adresu (10.0.0.4) a klikněte na **Uložit**.
    1. Pomocí stejných kroků přidejte další IP adresy (10.0.0.5).

1. V našem scénáři byla služba Azure AD-DS nakonfigurovaná tak, aby používala IP adresy 10.0.0.4 a 10.0.0.5, přičemž na obrázku níže je nastavená stejná IP adresa ve virtuální síti AADDS, jak ukazuje následující obrázek.

    ![Zobrazit vlastní servery DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpečení provozu protokolu LDAP

Protokol LDAP (Lightweight Directory Access Protocol) se používá ke čtení a zápisu do služby Active Directory. Komunikace s protokolem LDAP je důvěrná a zabezpečená pomocí technologie SSL (Secure Sockets Layer) (SSL)/technologie TLS (Transport Layer Security). Pomocí protokolu LDAP přes SSL (LDAPs) můžete povolit instalaci správně formátovaného certifikátu.

Další informace o zabezpečeném protokolu LDAP najdete v tématu [Konfigurace zabezpečení LDAP (LDAPS) pro Azure AD Domain Services spravovanou doménu](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

V této části vytvoříte certifikát podepsaný svým držitelem, stáhnete certifikát a nakonfigurujete zabezpečený protokol LDAP (LDAPs) pro spravovanou doménu **hdifabrikam** Azure AD-DS.

Následující skript vytvoří certifikát pro hdifabrikam. Certifikát je uložený pod cestou "LocalMachine".

> [!Note] 
> K vytvoření žádosti o certifikát SSL lze použít jakýkoli \#nástroj nebo aplikaci, která vytvoří platný požadavek PKCS 10.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Ověřte, že je certifikát nainstalovaný v osobním úložišti\'počítače s. Proveďte následující kroky:

1. Spusťte konzolu MMC (Microsoft Management Console).
1. Přidejte modul snap-in Certifikáty, který spravuje certifikáty v místním počítači.
1. Rozbalte položku **certifikáty (místní počítač)** , rozbalte položku **osobní**a poté rozbalte položku **certifikáty**. V osobním úložišti by měl existovat nový certifikát. Tento certifikát je vydaný plně kvalifikovanému názvu hostitele.

    ![ověřit vytvoření certifikátu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. V pravém podokně klikněte pravým tlačítkem na certifikát, který jste vytvořili v předchozím kroku, přejděte na **všechny úlohy**a klikněte na **exportovat**.

1. Na stránce **exportovat privátní klíč** klikněte na **Ano, exportujte privátní klíč**. Pro čtení šifrovaných zpráv z počítače, do kterého se klíč importuje, se vyžaduje privátní klíč.

    ![exportovat privátní klíč](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stránce **Formát** souboru pro export ponechte výchozí nastavení a klikněte na tlačítko **Další**. 
1. Na stránce **heslo** zadejte heslo privátního klíče, vyberte možnost **TripleDES-SHA1** pro **šifrování** a klikněte na tlačítko **Další**.
1. Na stránce **soubor pro export** zadejte cestu a název exportovaného souboru certifikátu a potom klikněte na tlačítko **Další**.
1. Název souboru musí být přípona. pfx, tento soubor je nakonfigurován na Azure Portal pro navázání zabezpečeného připojení.
1. Povolte zabezpečený protokol LDAP (LDAPs) pro Azure AD Domain Services spravovanou doménu.
    1. Vyberte **HDIFabrikam.com** domény z Azure Portal.
    1. V části **Spravovat**klikněte na **protokol Secure LDAP** .
    1. Na obrazovce **protokol Secure LDAP** klikněte na **povolit** v části **protokol Secure LDAP**.
    1. Vyhledejte soubor certifikátu. pfx, který jste exportovali v počítači.
    1. Zadejte heslo certifikátu.

    ![Povolit zabezpečený protokol LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Teď, když jste povolili protokol Secure LDAP, se ujistěte, že je dosažitelná povolením portu 636.
    1. Klikněte na skupinu zabezpečení sítě **AADDS-HDIFabrikam.com-NSG** ve skupině prostředků **HDIFabrikam-CentralUS** .
    1. V části **Nastavení** klikněte na **příchozí pravidla** > zabezpečení**Přidat**.
    1. Na obrazovce **Přidat pravidlo zabezpečení příchozího připojení** zadejte následující vlastnosti a klikněte na **Přidat**:

        | Vlastnost | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Cíl | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name | Port_LDAP_636 |

    ![Příchozí pravidlo zabezpečení](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`je uživatelem přiřazená spravovaná identita, role přispěvatele služby HDInsight Domain Services je povolená pro spravovanou identitu, která umožní této identitě číst, vytvářet, upravovat a odstraňovat operace služby Domain Services.

    ![Vytvořit spravovanou identitu přiřazenou uživatelem](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Vytváření clusteru HDInsight s povoleným Balíček zabezpečení podniku

Tento krok vyžaduje následující požadavky:

1. V tomto umístění `West US`vytvořte novou `HDIFabrikam-WestUS` skupinu prostředků.
1. Vytvořte virtuální síť, která bude hostovat cluster HDInsight s povoleným protokolem ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Vytvořte partnerský vztah mezi Virtual Network hostujícím AADDS (`HDIFabrikam-AADDSVNET`) a Virtual Network, která bude hostovat cluster HDInsight s povoleným protokolem ESP (`HDIFabrikam-HDIVNet`). K navázání partnerského vztahu těchto dvou virtuálních sítí použijte následující kód PowerShellu.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Vytvořte nový účet Azure Data Lake Storage Gen2 **Hdigen2store**, který je nakonfigurovaný pomocí uživatelsky spravované identity **HDIFabrikamManagedIdentity**. Další informace o vytváření účtů Data Lake Storage Gen2 s povolenými identitami spravovanými uživatelem najdete v tématu [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Nastavte vlastní DNS ve virtuální síti **HDIFabrikam-AADDSVNET** .
    1. Do **skupin** > prostředků služby Azure Portal >**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **servery DNS**.
    1. Vyberte **vlastní** a zadejte `10.0.0.4` a `10.0.0.5`.
    1. Klikněte na **Uložit**.

        ![Uložit vlastní nastavení DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Vytvořte nový cluster HDInsight Spark s povoleným protokolem ESP.
    1. Klikněte na **vlastní (velikost, nastavení, aplikace)** .
    2. Zadejte požadované podrobnosti o **základech**oddílu 1. Zajistěte, aby byl **cluster typu** **Spark 2,3 (HDI 3,6)** a **Skupina prostředků** je **HDIFabrikam-CentralUS** .

    1. V části 2 **zabezpečení a sítě**proveďte následující kroky:
        1. V části **balíček zabezpečení podniku**klikněte na **povoleno** .
        1. Klikněte na **Správce clusteru uživatel** a vyberte účet **HDIAdmin** , který jste vytvořili dříve jako místní uživatel s oprávněními správce. Klikněte na tlačítko **vyberte**.

        1. Klikněte na **přístupová Skupina clusteru** a pak vyberte **HDIUserGroup**. Každý uživatel, který přidáte do této skupiny v budoucnosti, bude mít přístup k clusterům HDInsight.

            ![vybrat skupinu přístupu clusteru](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Proveďte další kroky konfigurace clusteru a ověřte podrobnosti o **souhrnu clusteru**. Klikněte na možnost **Vytvořit**.

1. Přihlaste se k uživatelskému rozhraní Ambari pro nově vytvořený `https://CLUSTERNAME.azurehdinsight.net` cluster pomocí uživatelského jména `hdiadmin@hdifabrikam.com` a hesla správce.

    ![Přihlásit se k Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Na řídicím panelu clusteru klikněte na **role** .
1. Na stránce **role** zadejte skupinu **hdiusergroup** , kterou chcete přiřadit k roli **Správce clusteru** v části **přiřazení rolí k těmto funkcím**.

    ![Přiřazení role Správce clusteru k hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otevřete svého klienta SSH a přihlaste se ke clusteru pomocí **hdiuser** , který jste vytvořili dříve v místní službě Active Directory.

    ![Přihlaste se ke clusteru pomocí SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Pokud se k tomuto účtu přihlašujete, pak jste správně nakonfigurovali cluster ESP pro synchronizaci s místní službou Active Directory.

## <a name="next-steps"></a>Další postup

* [Úvod k zabezpečení Apache Hadoop pomocí Balíček zabezpečení podniku](hdinsight-security-overview.md)
