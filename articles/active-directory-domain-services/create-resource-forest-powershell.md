---
title: Vytvoření Azure AD Domain Services doménové struktury prostředků pomocí Azure PowerShell | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit a nakonfigurovat Azure Active Directory Domain Services doménovou strukturu prostředků a odchozí doménovou strukturu do místního prostředí Active Directory Domain Services pomocí Azure PowerShell.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619963"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Vytvoření doménové struktury prostředků Azure Active Directory Domain Services a odchozího vztahu důvěryhodnosti doménové struktury do místní domény pomocí Azure PowerShell

V prostředích, kde nemůžete synchronizovat hodnoty hash hesel, nebo máte uživatele, kteří se výhradně přihlásili pomocí čipových karet, aby si neznali heslo, můžete v Azure Active Directory Domain Services (Azure služba AD DS) použít doménovou strukturu prostředků. Doménová struktura prostředků používá jednosměrný odchozí vztah důvěryhodnosti z Azure služba AD DS do jednoho nebo více místních služba AD DS prostředí. Tento vztah důvěryhodnosti umožňuje uživatelům, aplikacím a počítačům provádět ověřování v místní doméně ze spravované domény Azure služba AD DS. V doménové struktuře prostředků nejsou hodnoty hash místních hesel nikdy synchronizovány.

![Diagram vztahu důvěryhodnosti doménové struktury z Azure služba AD DS do místního služba AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření doménové struktury prostředků Azure služba AD DS pomocí Azure PowerShell
> * Vytvoření jednosměrné důvěryhodnosti pro odchozí doménovou strukturu ve spravované doméně pomocí Azure PowerShell
> * Konfigurace DNS v místním prostředí služba AD DS pro podporu připojení spravované domény
> * Vytvoření jednosměrného vztahu důvěryhodnosti pro příchozí doménovou strukturu v místním prostředí služba AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> Doménové struktury prostředků spravované domény aktuálně nepodporují Azure HDInsight ani soubory Azure. Výchozí doménové struktury uživatelů spravované domény podporují obě tyto další služby.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].

* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se ke svému předplatnému Azure přihlašujete pomocí rutiny [Connect-AzAccount][Connect-AzAccount] .
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se ke službě Azure AD](/powershell/azure/active-directory/install-adv2).
    * Pomocí rutiny [Connect-AzureAD][Connect-AzureAD] se ujistěte, že se přihlašujete k TENANTOVI Azure AD.
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto článku vytvoříte a nakonfigurujete odchozí vztah důvěryhodnosti doménové struktury ze spravované domény pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="deployment-process"></a>Proces nasazení

Jedná se o proces s více částmi k vytvoření doménové struktury prostředků spravované domény a vztahu důvěryhodnosti k místnímu služba AD DS. Následující kroky vysoké úrovně sestavují vaše důvěryhodné hybridní prostředí:

1. Vytvořte objekt služby spravované domény.
1. Vytvořte doménovou strukturu prostředků spravované domény.
1. Vytvořte hybridní připojení k síti pomocí sítě VPN typu Site-to-site nebo Express Route.
1. Vytvořte stranu spravované domény vztahu důvěryhodnosti.
1. Vytvořte místní služba AD DS stranu vztahu důvěryhodnosti.

Než začnete, ujistěte se, že rozumíte [hlediskům sítě, pojmenování doménové struktury a požadavkům DNS](tutorial-create-forest-trust.md#networking-considerations). Po nasazení se název doménové struktury spravované domény nedá změnit.

## <a name="create-the-azure-ad-service-principal"></a>Vytvoření instančního objektu služby Azure AD

Azure služba AD DS vyžaduje, aby instanční objekt synchronizoval data ze služby Azure AD. Tento objekt zabezpečení musí být vytvořený v tenantovi Azure AD před tím, než jste vytvořili doménovou strukturu prostředků spravované domény.

Vytvoření instančního objektu služby Azure AD pro Azure služba AD DS, který bude komunikovat a ověřovat sám sebe. Konkrétní ID aplikace se používá s názvem *služby řadiče domény* s ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Neměňte toto ID aplikace.

Pomocí rutiny [New-AzureADServicePrincipal][New-AzureADServicePrincipal] vytvořte instanční objekt služby Azure AD:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Vytvoření doménové struktury prostředků spravované domény

Pokud chcete vytvořit doménovou strukturu prostředků spravované domény, použijte `New-AzureAaddsForest` skript. Tento skript je součástí širší sady příkazů, které podporují vytváření a správu doménových struktur prostředků spravované domény, včetně vytvoření jednosměrně vázané doménové struktury v následující části. Tyto skripty jsou dostupné ze [Galerie prostředí PowerShell](https://www.powershellgallery.com/) a jsou digitálně podepsané technickým týmem Azure AD.

1. Nejdřív vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup][New-AzResourceGroup] . V následujícím příkladu má skupina prostředků název *myResourceGroup* a je vytvořená v oblasti *westus* . Použijte svůj vlastní název a požadovanou oblast:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Nainstalujte `New-AaddsResourceForestTrust` skript z [Galerie prostředí PowerShell][powershell-gallery] pomocí rutiny [install-Script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Zkontrolujte následující parametry potřebné pro tento `New-AzureAaddsForest` skript. Ujistěte se, že máte také požadované **Azure PowerShell** a moduly **Azure AD PowerShellu** . Ujistěte se, že jste naplánovali požadavky virtuální sítě, aby poskytovaly aplikace a místní připojení.

    | Name                         | Parametr skriptu          | Popis |
    |:-----------------------------|---------------------------|:------------|
    | Předplatné                 | *– azureSubscriptionId*    | ID předplatného, které se používá pro fakturaci Azure služba AD DS Seznam předplatných můžete získat pomocí rutiny [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Skupina prostředků               | *-aaddsResourceGroupName* | Název skupiny prostředků pro spravovanou doménu a přidružené prostředky. |
    | Umístění                     | *-aaddsLocation*          | Oblast Azure pro hostování spravované domény. Dostupné oblasti najdete v tématu [podporované oblasti pro Azure služba AD DS.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Správce Azure služba AD DS    | *-aaddsAdminUser*         | Hlavní název uživatele prvního spravovaného správce domény. Tento účet musí být ve vašem Azure Active Directory existujícím účtem cloudového uživatele. Uživatel a uživatel, který spouští skript, se přidají do skupiny *Administrators řadiče domény AAD* . |
    | Název domény pro Azure služba AD DS      | *-aaddsDomainName*        | Plně kvalifikovaný název domény spravované domény založený na předchozích pokynech k výběru názvu doménové struktury. |

    `New-AzureAaddsForest`Skript může vytvořit virtuální síť Azure a podsíť azure služba AD DS, pokud tyto prostředky ještě neexistují. Skript může volitelně vytvořit podsítě úloh, pokud jsou zadané:

    | Name                              | Parametr skriptu                  | Description |
    |:----------------------------------|:----------------------------------|:------------|
    | Název virtuální sítě              | *-aaddsVnetName*                  | Název virtuální sítě pro spravovanou doménu.|
    | Adresní prostor                     | *-aaddsVnetCIDRAddressSpace*      | Rozsah adres virtuální sítě v zápisu CIDR (při vytváření virtuální sítě).|
    | Název podsítě pro Azure služba AD DS           | *-aaddsSubnetName*                | Název podsítě virtuální sítě *aaddsVnetName* , která je hostitelem spravované domény. Do této podsítě nesaďte své vlastní virtuální počítače a úlohy. |
    | Rozsah adres Azure služba AD DS         | *-aaddsSubnetCIDRAddressRange*    | Rozsah adres podsítě v zápisu CIDR pro instanci služby AAD DS, například *192.168.1.0/24*. Rozsah adres musí být obsažený v rozsahu adres virtuální sítě a jiný než ostatní podsítě. |
    | Název podsítě úloh (volitelné)   | *-workloadSubnetName*             | Volitelný název podsítě ve virtuální síti *aaddsVnetName* , která se má vytvořit pro vlastní aplikační úlohy. Virtuální počítače a aplikace a také připojené k virtuální síti Azure s partnerským vztahem. |
    | Rozsah adres úloh (volitelné) | *-workloadSubnetCIDRAddressRange* | Volitelný rozsah adres podsítě v zápisu CIDR pro úlohy aplikace, například *192.168.2.0/24*. Rozsah adres musí být obsažený v rozsahu adres virtuální sítě a jiný než ostatní podsítě.|

1. Teď pomocí skriptu vytvořte doménovou strukturu prostředků spravované domény `New-AzureAaaddsForest` . Následující příklad vytvoří doménovou strukturu s názvem *addscontoso.com* a vytvoří podsíť úloh. Zadejte vlastní názvy parametrů a rozsahy IP adres nebo existující virtuální sítě.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Vytvoření doménové struktury prostředků spravované domény a podpůrných prostředků trvá poměrně dlouho. Umožněte dokončení skriptu. Pokračujte k další části a nakonfigurujte vaše místní připojení k síti, zatímco doménové struktury prostředků Azure AD na pozadí zřídí.

## <a name="configure-and-validate-network-settings"></a>Konfigurace a ověření nastavení sítě

Jak spravovaná doména pokračuje v nasazení, nakonfigurujte a ověřte připojení k hybridní síti v místním datovém centru. K použití se spravovanou doménou je také potřeba virtuální počítač pro správu, který slouží k pravidelné údržbě. Některá z hybridního připojení už možná existují ve vašem prostředí nebo možná budete muset ve svém týmu spolupracovat s ostatními a nakonfigurovat připojení.

Než začnete, ujistěte se, že rozumíte [hlediskům a doporučením sítě](tutorial-create-forest-trust.md#networking-considerations).

1. Vytvořte hybridní připojení k místní síti do Azure pomocí připojení Azure VPN nebo Azure ExpressRoute. Konfigurace hybridní sítě překračuje rozsah této dokumentace a ve vašem prostředí už možná existují. Podrobnosti o konkrétních scénářích najdete v následujících článcích:

    * [Síť VPN typu Site-to-site](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Přehled služby Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Pokud vytvoříte připojení přímo k virtuální síti spravované domény, použijte samostatnou podsíť brány. Nevytvářejte bránu v podsíti spravované domény.

1. Pokud chcete spravovat spravovanou doménu, vytvořte virtuální počítač pro správu, připojte ho ke spravované doméně a nainstalujte požadované nástroje pro správu služba AD DS.

    Během nasazování doménové struktury prostředků spravované domény [vytvořte virtuální počítač s Windows serverem](./join-windows-vm.md) a nainstalujte si [základní služba AD DS nástroje pro správu](./tutorial-create-management-vm.md) , abyste nainstalovali potřebné nástroje pro správu. Počkejte, až se virtuální počítač pro správu připojí ke spravované doméně, až po úspěšném nasazení domény do jednoho z následujících kroků.

1. Ověřte síťové připojení mezi vaší místní sítí a virtuální sítí Azure.

    * Ujistěte se, že se Váš místní řadič domény může připojit ke spravovanému virtuálnímu počítači pomocí nástroje `ping` nebo vzdálené plochy, například.
    * Ověřte, že se váš virtuální počítač pro správu může připojit k místním řadičům domény, a to pomocí nástroje, jako je například `ping` .

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, třeba *aaddscontoso.com* , a počkejte, než se stav nahlásí jako **spuštěný**.

    Když se spustí, [aktualizujte nastavení DNS pro virtuální síť Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) a pak [Povolte uživatelské účty pro Azure služba AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) k finalizaci konfigurací pro doménovou strukturu spravované domény.

1. Poznamenejte si adresy DNS zobrazené na stránce Přehled. Tyto adresy budete potřebovat při konfiguraci místní strany služby Active Directory v rámci vztahu důvěryhodnosti v následující části.
1. Restartujte virtuální počítač pro správu, aby získal nové nastavení DNS, a pak [Připojte virtuální počítač ke spravované doméně](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Jakmile je virtuální počítač pro správu připojený ke spravované doméně, znovu se připojte pomocí vzdálené plochy.

    Z příkazového řádku použijte `nslookup` a název doménové struktury prostředků spravované domény pro ověření překladu názvů doménové struktury prostředků.

    ```console
    nslookup aaddscontoso.com
    ```

    Příkaz by měl vracet dvě IP adresy pro doménovou strukturu prostředků.

## <a name="create-the-forest-trust"></a>Vytvoření vztahu důvěryhodnosti doménové struktury

Vztah důvěryhodnosti doménové struktury má dvě části – jednosměrný odchozí vztah důvěryhodnosti v doménové struktuře prostředků spravované domény a jednosměrný příchozí vztah důvěryhodnosti doménové struktury v místní služba AD DS doménové struktuře. Ručně vytvoříte obě strany tohoto vztahu důvěryhodnosti. Při vytváření obou stran se uživatelé a prostředky můžou úspěšně ověřit pomocí vztahu důvěryhodnosti doménové struktury. Doménová struktura prostředků spravované domény podporuje až 5 1 odchozích vztahů důvěryhodnosti doménové struktury do místních doménových struktur.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Vytvoření strany spravované domény ve vztahu důvěryhodnosti

Pomocí `Add-AaddsResourceForestTrust` skriptu vytvořte stranu spravované domény vztahu důvěryhodnosti. Nejdřív nainstalujte `Add-AaddsResourceForestTrust` skript z [Galerie prostředí PowerShell][powershell-gallery] pomocí rutiny [install-Script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Nyní zadejte skript následující informace:

| Name                               | Parametr skriptu     | Description |
|:-----------------------------------|:---------------------|:------------|
| Název domény pro Azure služba AD DS            | *-ManagedDomainFqdn* | Plně kvalifikovaný název domény spravované domény, například *aaddscontoso.com* |
| Název domény v místním služba AD DS      | *-TrustFqdn*         | Plně kvalifikovaný název domény důvěryhodné doménové struktury, například *OnPrem.contoso.com* |
| Popisný název vztahu důvěryhodnosti                | *-TrustFriendlyName* | Popisný název vztahu důvěryhodnosti |
| Místní služba AD DS IP adresy DNS | *-TrustDnsIPs*       | Seznam IPv4 adres serveru DNS, které jsou v seznamu důvěryhodných domén odděleny čárkami. |
| Heslo vztahu důvěryhodnosti                     | *-TrustPassword*     | Složité heslo pro vztah důvěryhodnosti. Toto heslo se zadává taky při vytváření jednosměrného příchozího vztahu důvěryhodnosti v místních služba AD DS. |
| Přihlašovací údaje                        | *– Pověření*       | Přihlašovací údaje, které se používají k ověření v Azure. Uživatel musí být ve *skupině AAD DC Administrators*. Pokud není zadaný, skript se vyzve k ověření. |

Následující příklad vytvoří vztah důvěryhodnosti s názvem *myAzureADDSTrust* a *OnPrem.contoso.com*. Použijte vlastní názvy parametrů a hesla:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Zapamatujte si heslo vztahu důvěryhodnosti. Stejné heslo musíte použít při vytváření místní strany vztahu důvěryhodnosti.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurace DNS v místní doméně

Chcete-li správně přeložit spravovanou doménu z místního prostředí, bude pravděpodobně nutné přidat servery pro přeposílání na existující servery DNS. Pokud jste nenakonfigurovali místní prostředí pro komunikaci se spravovanou doménou, proveďte následující kroky z pracovní stanice pro správu pro místní doménu služba AD DS:

1. Vyberte **Spustit | Nástroje pro správu | Služba DNS**
1. Klikněte pravým tlačítkem na server DNS, jako je například *myAD01*, vyberte možnost **vlastnosti** .
1. Zvolte nástroje **pro přeposílání** a pak **Upravit** pro přidání dalších služeb pro dodávání.
1. Přidejte IP adresy spravované domény, například *10.0.1.4* a *10.0.1.5*.
1. Z místního příkazového řádku ověřte překlad IP adres pomocí **příkazu nslookup** názvu domény doménové struktury prostředku spravované domény. Například `Nslookup aaddscontoso.com` by měly vracet dvě IP adresy pro doménovou strukturu prostředků spravované domény.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Vytvoření vztahu důvěryhodnosti příchozí doménové struktury v místní doméně

Místní služba AD DS doména potřebuje příchozí vztah důvěryhodnosti doménové struktury pro spravovanou doménu. Tento vztah důvěryhodnosti musí být ručně vytvořen v místní doméně služba AD DS, a proto jej nelze vytvořit z Azure Portal.

Pokud chcete nakonfigurovat příchozí vztah důvěryhodnosti v místní doméně služba AD DS, proveďte následující kroky z pracovní stanice pro správu pro místní doménu služba AD DS:

1. Vyberte **Spustit | Nástroje pro správu | Domény a vztahy důvěryhodnosti služby Active Directory**
1. Klikněte pravým tlačítkem na doména, jako je například *OnPrem.contoso.com*, vyberte možnost **vlastnosti** .
1. Zvolte kartu **vztahy důvěryhodnosti** a pak **nový vztah důvěryhodnosti** .
1. Zadejte název spravované domény, třeba *aaddscontoso.com*, a pak vyberte **Další** .
1. Vyberte možnost vytvoření **vztahu důvěryhodnosti doménové struktury** a pak vytvořte **jednosměrné: příchozí** vztah důvěryhodnosti.
1. Vyberte, chcete-li vytvořit vztah důvěryhodnosti **pouze pro tuto doménu**. V dalším kroku vytvoříte vztah důvěryhodnosti v Azure Portal pro spravovanou doménu.
1. Zvolte možnost použití **ověřování v rámci doménové struktury** a pak zadejte a potvrďte heslo vztahu důvěryhodnosti. Stejné heslo je také zadáno v Azure Portal v další části.
1. Projděte několik dalších oken s výchozími možnostmi a zvolte možnost **Ne, Nepotvrzujte odchozí vztah důvěryhodnosti**. Vztah důvěryhodnosti nelze ověřit, protože účet delegovaného správce k doménové struktuře prostředků spravované domény nemá požadovaná oprávnění. Toto chování je záměrné.
1. Vyberte **Dokončit** .

## <a name="validate-resource-authentication"></a>Ověření ověřování prostředků

Následující běžné scénáře vám umožní ověřit, že vztah důvěryhodnosti doménové struktury správně ověřuje uživatele a přístup k prostředkům:

* [Ověřování místního uživatele z doménové struktury prostředků Azure služba AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Přístup k prostředkům v doménové struktuře prostředků Azure služba AD DS pomocí místního uživatele](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Povolit sdílení souborů a tiskáren](#enable-file-and-printer-sharing)
    * [Vytvoření skupiny zabezpečení a přidání členů](#create-a-security-group-and-add-members)
    * [Vytvoření sdílené složky pro přístup mezi doménovými strukturami](#create-a-file-share-for-cross-forest-access)
    * [Ověření ověřování mezi doménovými strukturami v prostředku](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Ověřování místního uživatele z doménové struktury prostředků Azure služba AD DS

Je potřeba, aby byl virtuální počítač s Windows serverem připojený k doméně spravované domény prostředků. Použijte tento virtuální počítač k otestování místního uživatele, který se může ověřit na virtuálním počítači.

1. Připojte se k virtuálnímu počítači s Windows serverem připojenému k doménové struktuře prostředků spravované domény pomocí přihlašovacích údajů pro vzdálenou plochu a správce spravované domény. Pokud se zobrazí chyba ověřování na úrovni sítě (NLA), ověřte, že uživatelský účet, který jste použili, není uživatelský účet domény.

    > [!TIP]
    > K zabezpečenému připojení k virtuálním počítačům připojeným k Azure AD Domain Services můžete použít [službu Azure bastionu Host](../bastion/bastion-overview.md) v podporovaných oblastech Azure.

1. Otevřete příkazový řádek a pomocí `whoami` příkazu Zobrazte rozlišující název aktuálně ověřeného uživatele:

    ```console
    whoami /fqdn
    ```

1. Použijte `runas` příkaz pro ověření uživatele z místní domény. V následujícím příkazu nahraďte `userUpn@trusteddomain.com` hlavní název uživatele (UPN) uživatele z důvěryhodné místní domény. V příkazu se zobrazí výzva k zadání hesla uživatele:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Pokud je ověření úspěšné, otevře se nový příkazový řádek. Název nového příkazového řádku zahrnuje `running as userUpn@trusteddomain.com` .
1. Pomocí `whoami /fqdn` příkazu na novém příkazovém řádku můžete zobrazit rozlišující název ověřeného uživatele z místní služby Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Přístup k prostředkům v doménové struktuře prostředků Azure služba AD DS pomocí místního uživatele

Pomocí virtuálního počítače s Windows serverem připojeného k doménové struktuře prostředků spravované domény můžete otestovat scénář, ve kterém můžou uživatelé získat přístup k prostředkům hostovaným v doménové struktuře prostředků při ověřování z počítačů v místní doméně s uživateli z místní domény. Následující příklady vám ukážou, jak vytvořit a otestovat různé běžné scénáře.

#### <a name="enable-file-and-printer-sharing"></a>Povolit sdílení souborů a tiskáren

1. Připojte se k virtuálnímu počítači s Windows serverem připojenému k doménové struktuře prostředků spravované domény pomocí přihlašovacích údajů pro vzdálenou plochu a správce spravované domény. Pokud se zobrazí chyba ověřování na úrovni sítě (NLA), ověřte, že uživatelský účet, který jste použili, není uživatelský účet domény.

    > [!TIP]
    > K zabezpečenému připojení k virtuálním počítačům připojeným k Azure AD Domain Services můžete použít [službu Azure bastionu Host](../bastion/bastion-overview.md) v podporovaných oblastech Azure.

1. Otevřete **nastavení systému Windows**, vyhledejte a vyberte **Centrum síťových a sdílení**.
1. Vyberte možnost pro **změnu pokročilého nastavení sdílení** .
1. V části **Profil domény** vyberte **zapnout sdílení souborů a tiskáren** a pak **změny uložte**.
1. Zavřete **Centrum síťových a sdílení**.

#### <a name="create-a-security-group-and-add-members"></a>Vytvoření skupiny zabezpečení a přidání členů

1. Otevřete položku **Uživatelé a počítače služby Active Directory**.
1. Klikněte pravým tlačítkem myši na název domény, vyberte možnost **Nový** a pak vyberte možnost **organizační jednotka**.
1. Do pole název zadejte *LocalObjects* a pak vyberte **OK**.
1. V navigačním podokně vyberte a klikněte pravým tlačítkem na **LocalObjects** . Vyberte **Nový** a potom **Skupina**.
1. Do pole **název skupiny** zadejte *FileServerAccess* . V poli **Rozsah skupiny** vyberte **místní doména** a pak zvolte **OK**.
1. V podokně obsah poklikejte na **FileServerAccess**. Vyberte možnost **Členové**, zvolte možnost **Přidat** a potom vyberte **umístění**.
1. V zobrazení **umístění** vyberte místní službu Active Directory a pak zvolte **OK**.
1. Do pole **Zadejte názvy objektů k výběru** zadejte *Domain Users* . Vyberte možnost **kontrolovat jména**, zadejte přihlašovací údaje pro místní službu Active Directory a pak vyberte **OK**.

    > [!NOTE]
    > Je nutné zadat přihlašovací údaje, protože vztah důvěryhodnosti je pouze jedním ze způsobů. To znamená, že uživatelé ze spravované domény nemůžou získat přístup k prostředkům nebo Hledat uživatele nebo skupiny v důvěryhodné (místní) doméně.

1. Skupina **Domain Users** z vaší místní služby Active Directory by měla být členem skupiny **FileServerAccess** . Výběrem **OK** uložte skupinu a zavřete okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Vytvoření sdílené složky pro přístup mezi doménovými strukturami

1. Na virtuálním počítači s Windows serverem připojeném k doménové struktuře spravované domény vytvořte složku a zadejte její název, třeba *CrossForestShare*.
1. Klepněte pravým tlačítkem myši na složku a vyberte možnost **vlastnosti**.
1. Vyberte kartu **zabezpečení** a pak zvolte **Upravit**.
1. V dialogovém okně *oprávnění pro CrossForestShare* vyberte **Přidat**.
1. Do pole **Zadejte názvy objektů k výběru** zadejte *FileServerAccess* a pak vyberte **OK**.
1. V seznamu **skupiny nebo jména uživatelů** vyberte *FileServerAccess* . V seznamu **oprávnění pro FileServerAccess** zvolte možnost *Povolení* oprávnění k **úpravám** a **zápisu** a pak vyberte **OK**.
1. Vyberte kartu **sdílení** a pak zvolte **Rozšířené sdílení...**
1. Zvolte **sdílet tuto složku** a pak zadejte zapamatovatelné jméno sdílené složky v **názvu sdílené složky** , například *CrossForestShare*.
1. Vyberte **Oprávnění**. V seznamu **oprávnění pro všechny** vyberte možnost **udělit** oprávnění ke **změně** .
1. Dvakrát klikněte na **OK** a pak na **Zavřít**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Ověření ověřování mezi doménovými strukturami v prostředku

1. Přihlaste se k počítači s Windows připojenému k místní službě Active Directory pomocí uživatelského účtu z vaší místní služby Active Directory.
1. Pomocí **Průzkumníka Windows** se připojte ke sdílené složce, kterou jste vytvořili, pomocí plně kvalifikovaného názvu hostitele a sdílené složky, jako je například `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Chcete-li ověřit oprávnění k zápisu, ve složce klikněte pravým tlačítkem myši, vyberte možnost **Nový** a pak vyberte možnost **textový dokument**. Použijte výchozí název **nový textový dokument**.

    Pokud jsou oprávnění k zápisu nastavena správně, je vytvořen nový textový dokument. Následující kroky pak otevřou, upraví a odstraní soubor podle potřeby.
1. Chcete-li ověřit oprávnění ke čtení, otevřete **nový textový dokument**.
1. Chcete-li ověřit oprávnění upravit, přidejte text do souboru a ukončete **Poznámkový blok**. Po zobrazení výzvy k uložení změn klikněte na **Uložit**.
1. Chcete-li ověřit oprávnění k odstranění, klikněte pravým tlačítkem myši na **nový textový dokument** a zvolte možnost **Odstranit**. Kliknutím na **tlačítko Ano** potvrďte odstranění souboru.

## <a name="update-or-remove-outbound-forest-trust"></a>Aktualizace nebo odebrání vztahu důvěryhodnosti pro odchozí doménové struktury

Pokud potřebujete aktualizovat existující jednosměrnou odchozí doménovou strukturu ze spravované domény, můžete použít `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` skripty a. Tyto skripty pomůžou ve scénářích, kdy chcete aktualizovat popisný název vztahu důvěryhodnosti doménové struktury nebo heslo vztahu důvěryhodnosti. Chcete-li odebrat jednosměrný odchozí vztah důvěryhodnosti ze spravované domény, můžete použít `Remove-AaddsResourceForestTrust` skript. V přidružené místní doménové struktuře služba AD DS musíte ručně odebrat jednosměrný příchozí vztah důvěryhodnosti doménové struktury.

### <a name="update-a-forest-trust"></a>Aktualizace vztahu důvěryhodnosti doménové struktury

V běžném provozu doménová struktura spravované domény a místní doménové struktury vyjednávají běžný proces aktualizace hesla mezi sebou. To je součástí procesu zabezpečení vztahu důvěryhodnosti normálního služba AD DS. Heslo vztahu důvěryhodnosti nemusíte ručně střídat, pokud u vztahu důvěryhodnosti nedochází k potížím a chcete ho ručně resetovat na známé heslo. Další informace najdete v tématu [změny hesla k objektu důvěryhodné domény](concepts-forest-trust.md#tdo-password-changes).

Následující příklady kroků ukazují, jak aktualizovat existující vztah důvěryhodnosti, pokud potřebujete ručně resetovat heslo odchozího vztahu důvěryhodnosti:

1. `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` Pomocí rutiny [install-Script][Install-Script] nainstalujte ze [Galerie prostředí PowerShell][powershell-gallery] skripty a:

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Předtím, než budete moci aktualizovat existující vztah důvěryhodnosti, nejprve získejte prostředek důvěryhodnosti pomocí `Get-AaddsResourceForestTrusts` skriptu. V následujícím příkladu je existující vztah důvěryhodnosti přiřazen k objektu s názvem *existingTrust*. Zadejte název vlastní spravované doménové struktury domény a název místní doménové struktury, které chcete aktualizovat:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Chcete-li aktualizovat existující heslo vztahu důvěryhodnosti, použijte `Set-AaddsResourceForestTrust` skript. Zadejte existující objekt vztahu důvěryhodnosti z předchozího kroku a pak nové heslo vztahu důvěryhodnosti. Prostředí PowerShell neuplatňuje žádné složitosti hesla, takže se ujistěte, že jste pro své prostředí vygenerovali a použili zabezpečené heslo.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Odstranění vztahu důvěryhodnosti doménové struktury

Pokud již nepotřebujete jednosměrný odchozí vztah důvěryhodnosti doménové struktury ze spravované domény do místní doménové struktury služba AD DS, můžete ji odebrat. Před odebráním vztahu důvěryhodnosti se ujistěte, že nepotřebujete žádné aplikace ani služby ověřit proti místní služba AD DS doménové struktuře. Jednosměrný příchozí vztah důvěryhodnosti musíte ručně odebrat v místní služba AD DS doménové struktuře.

1. Nainstalujte `Remove-AaddsResourceForestTrust` skript z [Galerie prostředí PowerShell][powershell-gallery] pomocí rutiny [install-Script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Nyní odeberte vztah důvěryhodnosti doménové struktury pomocí `Remove-AaddsResourceForestTrust` skriptu. V následujícím příkladu je odebrána důvěryhodnost s názvem *myAzureADDSTrust* mezi spravovanou doménovou strukturou s názvem *aaddscontoso.com* a místní doménovou strukturou *OnPrem.contoso.com* . Zadejte název vlastní spravované doménové struktury domény a název místní doménové struktury, který chcete odebrat:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Chcete-li odebrat jednosměrný příchozí vztah důvěryhodnosti z místní doménové struktury služba AD DS, připojte se k počítači pro správu s přístupem k místní doménové struktuře služba AD DS a proveďte následující kroky:

1. Vyberte **Spustit | Nástroje pro správu | Domény a vztahy důvěryhodnosti služby Active Directory**
1. Klikněte pravým tlačítkem na doména, jako je například *OnPrem.contoso.com*, vyberte možnost **vlastnosti** .
1. Zvolte kartu **vztahy důvěryhodnosti** a pak vyberte existující příchozí vztah důvěryhodnosti z vaší spravované doménové struktury domény.
1. Vyberte **Odebrat** a potvrďte, že chcete odebrat příchozí vztah důvěryhodnosti.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření doménové struktury prostředků spravované domény pomocí Azure PowerShell
> * Vytvoření jednosměrné důvěryhodnosti pro odchozí doménovou strukturu ve spravované doméně pomocí Azure PowerShell
> * Konfigurace DNS v místním prostředí služba AD DS pro podporu připojení ke spravované doméně
> * Vytvoření jednosměrného vztahu důvěryhodnosti pro příchozí doménovou strukturu v místním prostředí služba AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Další koncepční informace o typech doménové struktury v Azure služba AD DS najdete v tématu [co jsou doménové struktury prostředků?][concepts-forest] a [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/