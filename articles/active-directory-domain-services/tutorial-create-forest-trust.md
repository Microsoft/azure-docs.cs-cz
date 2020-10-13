---
title: Kurz – vytvoření vztahu důvěryhodnosti doménové struktury v Azure AD Domain Services | Microsoft Docs
description: Naučte se vytvořit jednosměrnou odchozí doménovou strukturu do místní služba AD DS domény v Azure Portal pro Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: cbdcd170e6c6fb768172acfe3eb3c907714cd560
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967252"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Kurz: Vytvoření vztahu důvěryhodnosti odchozí doménové struktury do místní domény v Azure Active Directory Domain Services

V prostředích, kde nemůžete synchronizovat hodnoty hash hesel, nebo máte uživatele, kteří se výhradně přihlásili pomocí čipových karet, aby si neznali heslo, můžete v Azure Active Directory Domain Services (Azure služba AD DS) použít doménovou strukturu prostředků. Doménová struktura prostředků používá jednosměrný odchozí vztah důvěryhodnosti z Azure služba AD DS do jednoho nebo více místních služba AD DS prostředí. Tento vztah důvěryhodnosti umožňuje uživatelům, aplikacím a počítačům provádět ověřování v místní doméně ze spravované domény Azure služba AD DS. V doménové struktuře prostředků nejsou hodnoty hash místních hesel nikdy synchronizovány.

![Diagram vztahu důvěryhodnosti doménové struktury z Azure služba AD DS do místního služba AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace DNS v místním prostředí služba AD DS pro podporu připojení Azure služba AD DS
> * Vytvoření jednosměrného vztahu důvěryhodnosti pro příchozí doménovou strukturu v místním prostředí služba AD DS
> * Vytvoření jednosměrné důvěryhodnosti pro odchozí doménovou strukturu v Azure služba AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Azure Active Directory Domain Services spravovaná doména vytvořená pomocí doménové struktury prostředků a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Ujistěte se, že jste vytvořili spravovanou doménu pomocí doménové struktury *prostředků* . Výchozí možnost vytvoří doménovou strukturu *uživatele* . Pouze doménové struktury prostředků můžou vytvářet vztahy důvěryhodnosti s Prem služba AD DSmi prostředími.
    >
    > Pro spravovanou doménu je také nutné použít minimálně jednotku SKU *Enterprise* . V případě potřeby [změňte skladovou položku pro spravovanou doménu][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete odchozí vztah důvěryhodnosti doménové struktury z Azure služba AD DS pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="networking-considerations"></a>Aspekty sítí

Virtuální síť, která je hostitelem doménové struktury prostředků Azure služba AD DS, potřebuje síťové připojení k vaší místní službě Active Directory. Aplikace a služby také potřebují síťové připojení k virtuální síti hostující doménovou strukturu prostředků Azure služba AD DS. Síťové připojení k doménové struktuře prostředků Azure služba AD DS musí být vždycky zapnuté a stabilní, jinak se uživatelům nemusí podařit ověřit nebo získat přístup k prostředkům.

Před konfigurací vztahu důvěryhodnosti doménové struktury ve službě Azure služba AD DS se ujistěte, že vaše síť mezi Azure a místním prostředím splňuje následující požadavky:

* Použijte privátní IP adresy. Nespoléhá se na protokol DHCP s přiřazením dynamické IP adresy.
* Nepoužívejte překrývající se adresní prostory IP adres, aby bylo možné vytvořit partnerský vztah virtuálních sítí a směrování pro úspěšnou komunikaci mezi Azure a místním prostředím.
* Virtuální síť Azure vyžaduje podsíť brány pro konfiguraci připojení VPN nebo [ExpressRoute][expressroute] typu [site-to-Site (S2S)][vpn-gateway] .
* Vytvořte podsítě s dostatkem IP adres pro podporu vašeho scénáře.
* Ujistěte se, že je v Azure služba AD DS vlastní podsíť, nesdílejte tuto podsíť virtuální sítě s virtuálními počítači a službami aplikací.
* Partnerské virtuální sítě nejsou přenosné.
    * Partnerské vztahy virtuálních sítí Azure musí být vytvořené mezi všemi virtuálními sítěmi, které chcete použít pro vztah důvěryhodnosti doménové struktury prostředků Azure služba AD DS k místnímu služba AD DS prostředí.
* Poskytněte nepřetržité síťové připojení k místní doménové struktuře služby Active Directory. Nepoužívejte připojení na vyžádání.
* Zajistěte, aby mezi názvem doménové struktury prostředků Azure služba AD DS a názvem vaší místní doménové struktury služby Active Directory bylo překlad nepřetržitého překladu adres (DNS).

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurace DNS v místní doméně

Chcete-li správně přeložit spravovanou doménu z místního prostředí, bude pravděpodobně nutné přidat servery pro přeposílání na existující servery DNS. Pokud jste nenakonfigurovali místní prostředí pro komunikaci se spravovanou doménou, proveďte následující kroky z pracovní stanice pro správu pro místní doménu služba AD DS:

1. Vyberte **Spustit | Nástroje pro správu | Služba DNS**
1. Pravým tlačítkem vyberte server DNS, například *myAD01*, a pak vyberte **vlastnosti** .
1. Zvolte nástroje **pro přeposílání**a pak **Upravit** pro přidání dalších služeb pro dodávání.
1. Přidejte IP adresy spravované domény, například *10.0.2.4* a *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Vytvoření vztahu důvěryhodnosti příchozí doménové struktury v místní doméně

Místní služba AD DS doména potřebuje příchozí vztah důvěryhodnosti doménové struktury pro spravovanou doménu. Tento vztah důvěryhodnosti musí být ručně vytvořen v místní doméně služba AD DS, a proto jej nelze vytvořit z Azure Portal.

Pokud chcete nakonfigurovat příchozí vztah důvěryhodnosti v místní doméně služba AD DS, proveďte následující kroky z pracovní stanice pro správu pro místní doménu služba AD DS:

1. Vyberte **Spustit | Nástroje pro správu | Domény a vztahy důvěryhodnosti služby Active Directory**
1. Klikněte pravým tlačítkem na doména, jako je *OnPrem.contoso.com*, a pak vyberte **vlastnosti** .
1. Zvolte kartu **vztahy důvěryhodnosti** a pak **nový vztah důvěryhodnosti** .
1. Zadejte název domény Azure služba AD DS, jako je *aaddscontoso.com*, a pak vyberte **Další** .
1. Vyberte možnost vytvoření **vztahu důvěryhodnosti doménové struktury**a pak vytvořte **jednosměrné: příchozí** vztah důvěryhodnosti.
1. Vyberte, chcete-li vytvořit vztah důvěryhodnosti **pouze pro tuto doménu**. V dalším kroku vytvoříte vztah důvěryhodnosti v Azure Portal pro spravovanou doménu.
1. Zvolte možnost použití **ověřování v rámci doménové struktury**a pak zadejte a potvrďte heslo vztahu důvěryhodnosti. Stejné heslo je také zadáno v Azure Portal v další části.
1. Projděte několik dalších oken s výchozími možnostmi a zvolte možnost **Ne, Nepotvrzujte odchozí vztah důvěryhodnosti**.
1. Vyberte **Dokončit** .

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Vytvoření odchozího vztahu důvěryhodnosti doménové struktury v Azure služba AD DS

Když je místní služba AD DS doména nakonfigurovaná k překladu spravované domény a vytvořeného příchozího vztahu důvěryhodnosti doménové struktury, teď vytvoří odchozí vztah důvěryhodnosti doménové struktury. Tento odchozí vztah důvěryhodnosti doménové struktury dokončí vztah důvěryhodnosti mezi místní služba AD DSovou doménou a spravovanou doménou.

Chcete-li vytvořit odchozí vztah důvěryhodnosti pro spravovanou doménu v Azure Portal, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**a pak vyberte spravovanou doménu, například *aaddscontoso.com* .
1. V nabídce na levé straně spravované domény vyberte možnost **vztahy důvěryhodnosti**a pak zvolte možnost **Přidat** vztah důvěryhodnosti.

   > [!NOTE]
   > Pokud nevidíte možnost nabídky **důvěryhodnosti** , zkontrolujte v části **vlastnosti** pro *Typ doménové struktury*. Vztahy důvěryhodnosti můžou vytvářet jenom doménové struktury *prostředků* . Pokud je typem doménové struktury *uživatel*, nemůžete vytvořit vztahy důvěryhodnosti. V současné době neexistuje způsob, jak změnit typ doménové struktury spravované domény. Je nutné odstranit a znovu vytvořit spravovanou doménu jako doménovou strukturu prostředků.

1. Zadejte zobrazovaný název, který identifikuje vaši důvěryhodnost, a pak místní název DNS důvěryhodné doménové struktury, například *OnPrem.contoso.com* .
1. Zadejte stejné heslo vztahu důvěryhodnosti, které bylo použito při konfiguraci vztahu důvěryhodnosti příchozí doménové struktury pro místní služba AD DS domény v předchozí části.
1. Poskytněte aspoň dva servery DNS místní domény služba AD DS, například *10.1.1.4* a *zákazníka 10.1.1.5* .
1. Až budete připraveni, **uložte** odchozí vztah důvěryhodnosti doménové struktury.

    ![Vytvořit vztah důvěryhodnosti odchozí doménové struktury v Azure Portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Ověření ověřování prostředků

Následující běžné scénáře vám umožní ověřit, že vztah důvěryhodnosti doménové struktury správně ověřuje uživatele a přístup k prostředkům:

* [Ověřování místního uživatele z doménové struktury prostředků Azure služba AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Přístup k prostředkům v doménové struktuře prostředků Azure služba AD DS pomocí místního uživatele](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Povolit sdílení souborů a tiskáren](#enable-file-and-printer-sharing)
    * [Vytvoření skupiny zabezpečení a přidání členů](#create-a-security-group-and-add-members)
    * [Vytvoření sdílené složky pro přístup mezi doménovými strukturami](#create-a-file-share-for-cross-forest-access)
    * [Ověření ověřování mezi doménovými strukturami v prostředku](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Ověřování místního uživatele z doménové struktury prostředků Azure služba AD DS

K spravované doméně byste měli připojit virtuální počítač s Windows serverem. Použijte tento virtuální počítač k otestování místního uživatele, který se může ověřit na virtuálním počítači. V případě potřeby [vytvořte virtuální počítač s Windows a připojte ho ke spravované doméně][join-windows-vm].

1. Připojte se k virtuálnímu počítači s Windows serverem připojenému k doménové struktuře prostředků Azure služba AD DS pomocí [Azure bastionu](../bastion/bastion-overview.md) a vašich přihlašovacích údajů správce Azure služba AD DS.
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

Pomocí virtuálního počítače s Windows serverem připojeného k doménové struktuře prostředků Azure služba AD DS můžete otestovat scénář, ve kterém můžou uživatelé přistupovat k prostředkům hostovaným v doménové struktuře prostředků v případě, že se ověřují z počítačů v místní doméně s uživateli z místní domény. Následující příklady vám ukážou, jak vytvořit a otestovat různé běžné scénáře.

#### <a name="enable-file-and-printer-sharing"></a>Povolit sdílení souborů a tiskáren

1. Připojte se k virtuálnímu počítači s Windows serverem připojenému k doménové struktuře prostředků Azure služba AD DS pomocí [Azure bastionu](../bastion/bastion-overview.md) a vašich přihlašovacích údajů správce Azure služba AD DS.

1. Otevřete **nastavení systému Windows**, vyhledejte a vyberte **Centrum síťových a sdílení**.
1. Vyberte možnost pro **změnu pokročilého nastavení sdílení** .
1. V části **Profil domény**vyberte **zapnout sdílení souborů a tiskáren** a pak **změny uložte**.
1. Zavřete **Centrum síťových a sdílení**.

#### <a name="create-a-security-group-and-add-members"></a>Vytvoření skupiny zabezpečení a přidání členů

1. Otevřete položku **Uživatelé a počítače služby Active Directory**.
1. Klikněte pravým tlačítkem myši na název domény, vyberte možnost **Nový**a pak vyberte možnost **organizační jednotka**.
1. Do pole název zadejte *LocalObjects*a pak vyberte **OK**.
1. V navigačním podokně vyberte a klikněte pravým tlačítkem na **LocalObjects** . Vyberte **Nový** a potom **Skupina**.
1. Do pole **název skupiny** zadejte *FileServerAccess* . V poli **Rozsah skupiny**vyberte **místní doména**a pak zvolte **OK**.
1. V podokně obsah poklikejte na **FileServerAccess**. Vyberte možnost **Členové**, zvolte možnost **Přidat**a potom vyberte **umístění**.
1. V zobrazení **umístění** vyberte místní službu Active Directory a pak zvolte **OK**.
1. Do pole **Zadejte názvy objektů k výběru** zadejte *Domain Users* . Vyberte možnost **kontrolovat jména**, zadejte přihlašovací údaje pro místní službu Active Directory a pak vyberte **OK**.

    > [!NOTE]
    > Je nutné zadat přihlašovací údaje, protože vztah důvěryhodnosti je pouze jedním ze způsobů. To znamená, že uživatelé ze spravované domény Azure služba AD DS nemůžou získat přístup k prostředkům nebo Hledat uživatele nebo skupiny v důvěryhodné (místní) doméně.

1. Skupina **Domain Users** z vaší místní služby Active Directory by měla být členem skupiny **FileServerAccess** . Výběrem **OK** uložte skupinu a zavřete okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Vytvoření sdílené složky pro přístup mezi doménovými strukturami

1. Na virtuálním počítači s Windows serverem připojeném k doménové struktuře prostředků Azure služba AD DS vytvořte složku a zadejte její název, jako je například *CrossForestShare*.
1. Klepněte pravým tlačítkem myši na složku a vyberte možnost **vlastnosti**.
1. Vyberte kartu **zabezpečení** a pak zvolte **Upravit**.
1. V dialogovém okně *oprávnění pro CrossForestShare* vyberte **Přidat**.
1. Do pole **Zadejte názvy objektů k výběru**zadejte *FileServerAccess* a pak vyberte **OK**.
1. V seznamu **skupiny nebo jména uživatelů** vyberte *FileServerAccess* . V seznamu **oprávnění pro FileServerAccess** zvolte možnost *Povolení* oprávnění k **úpravám** a **zápisu** a pak vyberte **OK**.
1. Vyberte kartu **sdílení** a pak zvolte **Rozšířené sdílení...**
1. Zvolte **sdílet tuto složku**a pak zadejte zapamatovatelné jméno sdílené složky v **názvu sdílené složky** , například *CrossForestShare*.
1. Vyberte **oprávnění**. V seznamu **oprávnění pro všechny** vyberte možnost **udělit** oprávnění ke **změně** .
1. Dvakrát klikněte na **OK** a pak na **Zavřít**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Ověření ověřování mezi doménovými strukturami v prostředku

1. Přihlaste se k počítači s Windows připojenému k místní službě Active Directory pomocí uživatelského účtu z vaší místní služby Active Directory.
1. Pomocí **Průzkumníka Windows**se připojte ke sdílené složce, kterou jste vytvořili, pomocí plně kvalifikovaného názvu hostitele a sdílené složky, jako je například `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Chcete-li ověřit oprávnění k zápisu, ve složce klikněte pravým tlačítkem myši, vyberte možnost **Nový**a pak vyberte možnost **textový dokument**. Použijte výchozí název **nový textový dokument**.

    Pokud jsou oprávnění k zápisu nastavena správně, je vytvořen nový textový dokument. Následující kroky pak otevřou, upraví a odstraní soubor podle potřeby.
1. Chcete-li ověřit oprávnění ke čtení, otevřete **nový textový dokument**.
1. Chcete-li ověřit oprávnění upravit, přidejte text do souboru a ukončete **Poznámkový blok**. Po zobrazení výzvy k uložení změn klikněte na **Uložit**.
1. Chcete-li ověřit oprávnění k odstranění, klikněte pravým tlačítkem myši na **nový textový dokument** a zvolte možnost **Odstranit**. Kliknutím na **tlačítko Ano** potvrďte odstranění souboru.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace DNS v místním prostředí služba AD DS pro podporu připojení Azure služba AD DS
> * Vytvoření jednosměrného vztahu důvěryhodnosti pro příchozí doménovou strukturu v místním prostředí služba AD DS
> * Vytvoření jednosměrné důvěryhodnosti pro odchozí doménovou strukturu v Azure služba AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Další koncepční informace o typech doménové struktury v Azure služba AD DS najdete v tématu [co jsou doménové struktury prostředků?][concepts-forest] a [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md