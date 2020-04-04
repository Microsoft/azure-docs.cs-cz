---
title: Kurz – vytvoření vztahu důvěryhodnosti doménové struktury ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit jednosměrnou odchozí doménovou strukturu do místní domény služby AD DS na webu Azure Portal for Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9a76f72d3f01ab9253c452e49dde171280fe481d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654416"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Kurz: Vytvoření odchozího vztahu důvěryhodnosti doménové struktury pro místní doménu ve službě Azure Active Directory Domain Services (preview)

V prostředích, kde nelze synchronizovat zachycování hesel nebo máte uživatele, kteří se výhradně přihlašují pomocí čipových karet, aby neznali své heslo, můžete použít doménovou strukturu prostředků ve službě Azure Active Directory Domain Services (AD DS). Doménová struktura prostředků používá jednosměrný odchozí vztah důvěryhodnosti z Azure AD DS do jednoho nebo více místních prostředí služby AD DS. Tento vztah důvěryhodnosti umožňuje uživatelům, aplikacím a počítačům ověřit se v místní doméně ze spravované domény Služby Azure AD DS. Doménové struktury prostředků Azure AD DS jsou aktuálně ve verzi preview.

![Diagram důvěryhodnosti doménové struktury z Azure AD DS na místní službu AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace služby DNS v místním prostředí služby AD DS pro podporu připojení Azure AD DS
> * Vytvoření jednosměrného vztahu důvěryhodnosti doménové struktury v místním prostředí služby AD DS
> * Vytvoření jednosměrného odchozího vztahu důvěryhodnosti doménové struktury ve službě Azure AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména služby Azure Active Directory Domain Services vytvořená pomocí doménové struktury prostředků a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Ujistěte se, že vytvoříte spravovanou doménu Azure AD DS pomocí doménové struktury *prostředků.* Výchozí možnost vytvoří doménovou strukturu *uživatele.* Pouze doménové struktury prostředků můžete vytvořit vztahy důvěryhodnosti na prem prostředí ad DS. Pro spravovanou doménu je také nutné použít minimálně *spravovanou* položku rozlehlé sítě. V případě potřeby [změňte skladovou položku pro spravovanou doménu Azure AD DS][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a nakonfigurujete odchozí vztah důvěryhodnosti doménové struktury z Azure AD DS pomocí portálu Azure. Chcete-li začít, nejprve se přihlaste na [portál Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Aspekty sítí

Virtuální síť, která je hostitelem doménové struktury prostředků Služby Azure AD DS, potřebuje připojení k síti k místnímu službě Active Directory. Aplikace a služby také potřebují připojení k síti k virtuální síti, která je hostitelem doménové struktury prostředků Azure AD DS. Připojení k síti doménové struktury prostředků Azure AD DS musí být vždy zapnuté a stabilní, jinak uživatelé mohou selhat k ověření nebo přístupu k prostředkům.

Než nakonfigurujete vztah důvěryhodnosti doménové struktury ve službě Azure AD DS, ujistěte se, že vaše sítě mezi Azure a místním prostředím splňují následující požadavky:

* Používejte soukromé IP adresy. Nespoléhejte na službu DHCP s přiřazením dynamické adresy IP.
* Vyhněte se překrývání adresních prostorů IP, které umožní partnerský vztah a směrování virtuální chod virtuální sítě pro úspěšnou komunikaci mezi Azure a místním prostředím.
* Virtuální síť Azure potřebuje podsíť brány ke konfiguraci připojení VPN Azure [site-to-site (S2S)][vpn-gateway] nebo [ExpressRoute][expressroute]
* Vytvořte podsítě s dostatkem ADRES IP pro podporu vašeho scénáře.
* Ujistěte se, že Azure AD DS má vlastní podsíť, nesdílejte tuto podsíť virtuální sítě s virtuálními počítači a službami aplikací.
* Partnerské virtuální sítě nejsou přenosné.
    * Partnerské vztahy virtuální sítě Azure musí být vytvořeny mezi všemi virtuálními sítěmi, které chcete použít vztah důvěryhodnosti doménové struktury prostředků Služby Azure AD DS do místního prostředí služby AD DS.
* Poskytněte nepřetržité síťové připojení k místní doménové struktuře služby Active Directory. Nepoužívejte připojení na vyžádání.
* Ujistěte se, že mezi názvem doménové struktury prostředků služby Azure AD DS a místním názvem doménové struktury služby Active Directory je průběžné překlad názvů (DNS).

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurace služby DNS v místní doméně

Chcete-li správně vyřešit spravovanou doménu Azure AD DS z místního prostředí, budete muset přidat servery pro předávání na stávající servery DNS. Pokud jste nenakonfigurovali místní prostředí pro komunikaci se spravovanou doménou Azure AD DS, proveďte následující kroky z pracovní stanice pro správu pro místní doménu služby AD DS:

1. Vybrat **možnost Start | Nástroje pro správu | Služba DNS**
1. Vyberte dns server vpravo, například *myAD01*, vyberte **Vlastnosti**
1. Zvolte **Předávání ,** pak **Upravit** a přidejte další předávání.
1. Přidejte IP adresy spravované domény Azure AD DS, například *10.0.2.4* a *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Vytvoření vztahu důvěryhodnosti příchozí doménové struktury v místní doméně

Místní doména služby AD DS potřebuje příchozí vztah důvěryhodnosti doménové struktury pro spravovanou doménu Služby Azure AD DS. Tento vztah důvěryhodnosti musí být ručně vytvořen v místní doméně služby AD DS, nelze jej vytvořit z portálu Azure.

Chcete-li nakonfigurovat příchozí vztah důvěryhodnosti v místní doméně služby AD DS, proveďte následující kroky z pracovní stanice pro správu místní domény služby AD DS:

1. Vybrat **možnost Start | Nástroje pro správu | Domény a vztahy důvěryhodnosti služby Active Directory**
1. Doména vpravo, například *onprem.contoso.com*, vyberte **Vlastnosti**
1. Zvolte **Karta Vztahy důvěryhodnosti** a potom **Nový vztah důvěryhodnosti.**
1. Zadejte název v názvu domény Azure AD DS, například *aaddscontoso.com*, a pak vyberte **Další.**
1. Vyberte možnost vytvoření **vztahu důvěryhodnosti doménové struktury**a poté vytvořte **jednosměrný: příchozí** vztah důvěryhodnosti.
1. Zvolte, zda chcete vytvořit vztah důvěryhodnosti pouze pro **tuto doménu**. V dalším kroku vytvoříte vztah důvěryhodnosti na webu Azure Portal pro spravovanou doménu Azure AD DS.
1. Zvolte použití **ověřování v celé doménové struktuře**a zadejte a potvrďte důvěryhodné heslo. Stejné heslo se také zadává na webu Azure Portal v další části.
1. Projděte několik dalších oken s výchozími možnostmi a pak zvolte možnost **Ne, nepotvrzejte odchozí vztah důvěryhodnosti**.
1. Vybrat **dokončit**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Vytvoření odchozího vztahu důvěryhodnosti doménové struktury ve službě Azure AD DS

S místní doménou služby AD DS nakonfigurovanou k vyřešení spravované domény Služby Azure AD DS a vytvořeným vztahem důvěryhodnosti příchozí doménové struktury nyní vytvořili odchozí vztah důvěryhodnosti doménové struktury. Tento odchozí vztah důvěryhodnosti doménové struktury dokončí vztah důvěryhodnosti mezi místní doménou služby AD DS a spravovanou doménou Služby Azure AD DS.

Pokud chcete vytvořit odchozí vztah důvěryhodnosti pro spravovanou doménu Azure AD DS na webu Azure Portal, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby Azure AD Domain Services**a vyberte spravovanou doménu, například *aaddscontoso.com*
1. V nabídce na levé straně spravované domény Azure AD DS vyberte **Vztahy důvěryhodnosti**a pak zvolte **+ Přidat** vztah důvěryhodnosti.

   > [!NOTE]
   > Pokud možnost **Nabídka Vztahy důvěryhodnosti** nevidíte, zaškrtněte v části **Vlastnosti** *typu Doménová struktura*. Pouze *doménové* struktury prostředků mohou vytvářet vztahy důvěryhodnosti. Pokud je typ doménové struktury *Uživatel*, nelze vytvořit vztahy důvěryhodnosti. V současné době neexistuje žádný způsob, jak změnit typ doménové struktury spravované domény Azure AD DS. Je třeba odstranit a znovu vytvořit spravovanou doménu jako doménovou strukturu prostředků.

1. Zadejte zobrazovaný název, který identifikuje váš vztah důvěryhodnosti, a potom místní důvěryhodný název DNS doménové struktury, například *onprem.contoso.com*
1. Zadejte stejné heslo důvěryhodnosti, které bylo použito při konfiguraci vztahu důvěryhodnosti příchozí doménové struktury pro místní doménu služby AD DS v předchozí části.
1. Poskytněte alespoň dva servery DNS pro místní doménu služby AD DS, například *10.1.1.4* a *10.1.1.5*
1. Až budete připraveni, **uložte** vztah důvěryhodnosti odchozí doménové struktury

    ![Vytvoření odchozího vztahu důvěryhodnosti doménové struktury na webu Azure Portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Ověřit ověřování prostředků

Následující běžné scénáře umožňují ověřit, že vztah důvěryhodnosti doménové struktury správně ověřuje uživatele a přístup k prostředkům:

* [Místní ověřování uživatelů z doménové struktury prostředků Služby Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Přístup k prostředkům v doménové struktuře prostředků Azure AD DS pomocí místního uživatele](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Povolení sdílení souborů a tiskáren](#enable-file-and-printer-sharing)
    * [Vytvoření skupiny zabezpečení a přidání členů](#create-a-security-group-and-add-members)
    * [Vytvoření sdílené složky pro přístup mezi doménovými strukturami](#create-a-file-share-for-cross-forest-access)
    * [Ověření ověřování mezi doménovými strukturami pro prostředek](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Místní ověřování uživatelů z doménové struktury prostředků Služby Azure AD DS

K doméně prostředků Azure AD DS byste měli mít připojenou virtuální počítač Windows Serveru. Tento virtuální počítač slouží k testování místního uživatele, který se může na virtuálním počítači ověřit.

1. Připojte se k virtuálnímu počítači Windows Server, který je připojen k doménové struktuře prostředků Azure AD DS pomocí [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) a přihlašovacích údajů správce Služby Azure AD DS.
1. Otevřete příkazový řádek `whoami` a pomocí příkazu zobrazte rozlišující název aktuálně ověřeného uživatele:

    ```console
    whoami /fqdn
    ```

1. Pomocí `runas` příkazu ověřte jako uživatel z místní domény. V následujícím příkazu `userUpn@trusteddomain.com` nahraďte hlavní název uživatele z důvěryhodné místní domény. Příkaz zobrazí výzvu k zadání hesla uživatele:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Pokud je ověření úspěšné, otevře se nový příkazový řádek. Název nového příkazového `running as userUpn@trusteddomain.com`řádku obsahuje .
1. Pomocí `whoami /fqdn` nového příkazového řádku můžete zobrazit rozlišující název ověřeného uživatele z místní služby Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Přístup k prostředkům v doménové struktuře prostředků Azure AD DS pomocí místního uživatele

Pomocí virtuálního počítače Windows Server se připojil k doménové struktuře prostředků Azure AD DS, můžete otestovat scénář, kde uživatelé mají přístup k prostředkům hostovaným v doménové struktuře prostředků při ověřování z počítačů v místní doméně s uživateli z místní domény. Následující příklady ukazují, jak vytvořit a otestovat různé běžné scénáře.

#### <a name="enable-file-and-printer-sharing"></a>Povolení sdílení souborů a tiskáren

1. Připojte se k virtuálnímu počítači Windows Server, který je připojen k doménové struktuře prostředků Azure AD DS pomocí [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) a přihlašovacích údajů správce Služby Azure AD DS.

1. Spusťte **Nastavení systému Windows**a vyhledejte a vyberte **Centrum síťových připojení a sdílení**.
1. Zvolte možnost **Změnit upřesňující** nastavení sdílení.
1. V části **Profil domény**vyberte **Zapnout sdílení souborů a tiskáren a** potom Uložit **změny**.
1. Zavřete **Centrum síťových připojení a sdílení**.

#### <a name="create-a-security-group-and-add-members"></a>Vytvoření skupiny zabezpečení a přidání členů

1. Otevřete položku **Uživatelé a počítače služby Active Directory**.
1. Vyberte název domény vpravo, zvolte **Nový**a pak vyberte **Organizační jednotka**.
1. Do pole název zadejte *LocalObjects*a pak vyberte **OK**.
1. V navigačním podokně vyberte **položku LocalObjects** a klepněte na ni pravým tlačítkem myši. Vyberte **Možnost Nový** a potom **seskupit**.
1. Zadejte *FileServerAccess* do pole **Název skupiny.** V části **Obor skupiny**vyberte **možnost Doména místní**a pak zvolte **OK**.
1. V podokně obsahu poklepejte na **položku FileServerAccess**. Vyberte **Členové**, zvolte **přidat**a pak vyberte **Umístění**.
1. Vyberte místní službu Active Directory ze zobrazení **Umístění** a pak zvolte **OK**.
1. Do pole **Zadejte názvy objektů, které chcete vybrat,** zadejte uživatele Domain *Users.* Vyberte **Zkontrolovat názvy**, zadejte pověření pro místní službu Active Directory a vyberte **OK**.

    > [!NOTE]
    > Je nutné zadat pověření, protože vztah důvěryhodnosti je pouze jedním ze způsobů. To znamená, že uživatelé z Azure AD DS nemají přístup k prostředkům nebo vyhledávají uživatele nebo skupiny v důvěryhodné (místní) doméně.

1. Skupina **Domain Users** z místní služby Active Directory by měla být členem skupiny **FileServerAccess.** Výběrem **možnosti OK** skupinu uložíte a zavřete okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Vytvoření sdílené složky pro přístup mezi doménovými strukturami

1. Na virtuálním počítači se systémem Windows Server, který je připojen k doménové struktuře prostředků Azure AD DS, vytvořte složku a zadejte název, jako je *CrossForestShare*.
1. Vyberte složku vpravo a zvolte **Vlastnosti**.
1. Vyberte kartu **Zabezpečení** a pak zvolte **Upravit**.
1. V dialogovém *okně Oprávnění pro crossforestshare* vyberte **Přidat**.
1. Zadejte *SouborServerAccess* **v zadejte názvy objektů, které chcete vybrat**, a pak vyberte **OK**.
1. Vyberte *SouborServerAccess* ze seznamu **Skupiny nebo uživatelská jména.** V seznamu **Oprávnění pro fileserveraccess** zvolte *Povolit* oprávnění **Změnit** a **Zapisovat** a pak vyberte **OK**.
1. Vyberte kartu **Sdílení** a pak zvolte **Rozšířené sdílení...**
1. Zvolte **Sdílet tuto složku**a zadejte památný název sdílené složky do názvu sdílené **položky,** například *CrossForestShare*.
1. Vyberte **oprávnění**. V seznamu **Oprávnění pro všechny** zvolte **Povolit** oprávnění **Změnit.**
1. Vyberte **OK** dvakrát a potom **Zavřít**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Ověření ověřování mezi doménovými strukturami pro prostředek

1. Přihlaste se k počítači se systémem Windows, který je připojen k místní službě Active Directory, pomocí uživatelského účtu z místní služby Active Directory.
1. Pomocí **Průzkumníka Windows**se připojte ke sdílené složce, kterou `\\fs1.aaddscontoso.com\CrossforestShare`jste vytvořili, pomocí plně kvalifikovaného názvu hostitele a sdílené složky, například .
1. Chcete-li ověřit oprávnění k zápisu, vyberte ve složce vpravo, zvolte **Nový**a pak vyberte **Textový dokument**. Použijte výchozí název **Nový textový dokument**.

    Pokud jsou oprávnění k zápisu nastavena správně, vytvoří se nový textový dokument. Následující kroky pak soubor podle potřeby otevřou, upraví a odstraní.
1. Chcete-li ověřit oprávnění ke čtení, otevřete **možnost Nový textový dokument**.
1. Chcete-li ověřit oprávnění k úpravám, přidejte do souboru text a zavřete **poznámkový blok**. Po zobrazení výzvy k uložení změn zvolte **Uložit**.
1. Chcete-li ověřit oprávnění k odstranění, vyberte možnost **Nový textový dokument** a zvolte **Odstranit**. Chcete-li potvrdit odstranění souboru, zvolte **Ano.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace služby DNS v místním prostředí služby AD DS pro podporu připojení Azure AD DS
> * Vytvoření jednosměrného vztahu důvěryhodnosti doménové struktury v místním prostředí služby AD DS
> * Vytvoření jednosměrného odchozího vztahu důvěryhodnosti doménové struktury ve službě Azure AD DS
> * Testování a ověření vztahu důvěryhodnosti pro ověřování a přístup k prostředkům

Další rámcové informace o typech doménových struktur ve službě Azure [How do forest trusts work in Azure AD DS?][concepts-trust] AD DS najdete v tématech [Co jsou doménové struktury prostředků?][concepts-forest]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
