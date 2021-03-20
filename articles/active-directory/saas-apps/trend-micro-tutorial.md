---
title: 'Kurz: integrace služby pro jednotné přihlašování Azure AD s využitím Trend Micro Web Security (TMWS)'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008391"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s využitím Trend Micro Web Security (TMWS)

V tomto kurzu se dozvíte, jak integrovat Trend Micro Web Security (TMWS) s Azure Active Directory (Azure AD). Když integrujete TMWS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TMWS.
* Umožněte uživatelům, aby se automaticky přihlásili k TMWS pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné TMWS, které je povolené pro jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TMWS podporuje jednotné přihlašování iniciované v SP.
* Po nakonfigurování TMWS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. Informace o tom, jak vynutilit řízení relace pomocí Microsoft Cloud App Security, najdete v tématu připojení [a nasazení řízení podmíněného přístupu k aplikacím pro všechny aplikace](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Přidání TMWS z Galerie

Pokud chcete nakonfigurovat integraci TMWS do služby Azure AD, musíte přidat TMWS z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Trend Micro Web Security (TMWS)** .
1. Ve výsledcích hledání vyberte **Trend Micro Web Security (TMWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TMWS

Nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí TMWS pomocí testovacího uživatele s názvem B. Simon. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TMWS.

Tyto základní kroky dokončíte pro konfiguraci a testování jednotného přihlašování Azure AD pomocí TMWS:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , abyste funkci povolili uživatelům.
    1. [Vytvořte uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD.
    1. [Udělte testovacímu uživateli Azure AD](#grant-the-azure-ad-test-user-access-to-tmws) přístup k TMWS.
    1. [Nakonfigurujte nastavení synchronizace uživatelů a skupin ve službě Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Nakonfigurujte jednotné přihlašování TMWS](#configure-tmws-sso) na straně aplikace.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte konfiguraci.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Provedením těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **TMWS (Trend Micro Web Security)** v části **Spravovat** vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte tlačítko pera pro **základní konfiguraci SAML** , abyste mohli upravit nastavení:

   ![Upravit základní nastavení konfigurace SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty do následujících polí:

    a. Do pole **identifikátor (ID entity)** zadejte adresu URL v následujícím vzoru:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Do pole **Adresa URL odpovědi** zadejte tuto adresu URL:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Hodnota identifikátoru v předchozím kroku není hodnota, kterou byste měli zadat. Je nutné použít skutečný identifikátor. Tuto hodnotu můžete získat v části **Nastavení poskytovatele služeb portálu pro správu Azure** na stránce **metoda ověřování** pro Azure AD ze stránky **Správa > Directory Services**.

1. TMWS očekává kontrolní výrazy SAML v určitém formátu, takže musíte přidat mapování vlastních atributů do konfigurace atributů tokenu SAML. Tento snímek obrazovky ukazuje výchozí atributy:

    ![Výchozí atributy](common/default-attributes.png)

1. Kromě atributů na předchozím snímku obrazovky očekává TMWS dva další atributy, které mají být zpět v odpovědi SAML. Tyto atributy jsou uvedeny v následující tabulce. Atributy jsou předem vyplněné, ale můžete je změnit tak, aby splňovaly vaše požadavky.
    
    | Name | Zdrojový atribut|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | Názvu | User. userPrincipalName |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Pokud chcete stáhnout certifikát a uložit ho do počítače, vyberte odkaz pro **stažení** vedle tohoto názvu certifikátu:

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení TMWS (Trend Micro Web Security)** zkopírujte příslušnou adresu URL nebo adresy URL na základě vašich požadavků:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Vyberte **Uživatelé** a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte `B.Simon` .  
   1. Do pole **uživatelské jméno** zadejte **_username_ @* doménaspolečnosti *.* Přípona * * *. Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Udělte testovacímu uživateli Azure AD přístup k TMWS.

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TMWS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Trend Micro Web Security (TMWS)**.
1. Na stránce Přehled aplikace v části **Spravovat** vyberte **Uživatelé a skupiny**:

   ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurace nastavení synchronizace uživatelů a skupin ve službě Azure AD

1. V levém podokně vyberte **Azure Active Directory**.

1. V části **Spravovat** vyberte **Registrace aplikací** a potom v části **všechny aplikace** vyberte novou podnikovou aplikaci.

1. V části **Správa** vyberte **Certifikáty a tajné kódy**.

1. V oblasti **tajné klíče klienta** vyberte **nový tajný klíč klienta**.

1. Na **obrazovce Přidat tajný klíč klienta** Volitelně přidejte popis a vyberte období vypršení platnosti tajného klíče klienta a pak vyberte **Přidat**. Nový tajný klíč klienta se zobrazí v oblasti **tajné klíče klienta** .

1. Poznamenejte si hodnotu tajného klíče klienta. Později je zadáte do TMWS.

1. V části **Spravovat** vyberte **oprávnění rozhraní API**. 

1. V okně **oprávnění rozhraní API** vyberte **Přidat oprávnění**.

1. Na kartě **rozhraní API Microsoftu** v okně **požádat o oprávnění API** vyberte **Microsoft Graph** a pak na **oprávnění aplikace**.

1. Vyhledejte a přidejte tato oprávnění: 

    * Group.Read.All
    * User. Read. All

1. Vyberte **Přidat oprávnění**. Zobrazí se zpráva s potvrzením, že vaše nastavení bylo uloženo. Nová oprávnění se zobrazí v okně **oprávnění API** .

1. V oblasti **souhlasu udělit** vyberte **udělit souhlas správce pro *účet správce* (výchozí adresář)** a pak vyberte **Ano**. Zobrazí se zpráva s potvrzením, že byl udělen souhlas správce pro požadovaná oprávnění.

1. Vyberte **Přehled**. 

1. Poznamenejte si **ID aplikace (klienta)** a **ID adresáře (tenanta)** , které vidíte v pravém podokně. Později tyto informace zadáte do TMWS. Můžete také vybrat **vlastní názvy domén** v části **Azure Active Directory > spravovat** a zaznamenávat název domény, který se zobrazí v pravém podokně.

## <a name="configure-tmws-sso"></a>Konfigurace jednotného přihlašování TMWS

Provedením těchto kroků nakonfigurujete jednotné přihlašování TMWS na straně aplikace.

1. Přihlaste se ke konzole pro správu TMWS a pokračujte na **správu**  >  **Uživatelé & ověřování**  >  **adresářové služby**.

1. V horní části **obrazovky vyberte.**

1. Na stránce **metoda ověřování** vyberte **Azure AD**.

1. Výběrem možnosti **zapnuto** nebo **vypnuto** můžete nakonfigurovat, jestli chcete uživatelům Azure AD ve vaší organizaci NAVŠTĚVovat weby prostřednictvím TMWS, pokud jejich data nejsou synchronizovaná s TMWS.

    > [!NOTE]
    > Uživatele, kteří nejsou synchronizovaný ze služby Azure AD, se dají ověřovat jenom prostřednictvím známých bran TMWS nebo vyhrazeného portu pro vaši organizaci.

1. V části **nastavení zprostředkovatele identity** proveďte tyto kroky:

    a. Do pole **Adresa URL služby** zadejte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do pole **atribut přihlašovací jméno** zadejte **název deklarace identity uživatele** pomocí zdrojového atributu **User. onpremisessamaccountname** z Azure Portal.

    c. V poli **veřejný certifikát SSL** použijte stažený **certifikát (Base64)** z Azure Portal.

1. V části **Nastavení synchronizace** proveďte tyto kroky:

    a. V poli **tenant** zadejte do Azure Portal hodnotu **ID adresáře (tenant)** nebo **název vlastní domény** .

    b. Do pole **ID aplikace** zadejte hodnotu **ID aplikace (klienta)** z Azure Portal.

    c. Do pole **tajný klíč klienta** zadejte **tajný klíč klienta** z Azure Portal.

    d. Vyberte **plán synchronizace** , který se synchronizuje se službou Azure AD ručně, nebo podle plánu. Pokud vyberete možnost **ručně**, pokaždé, když dojde ke změnám informací o uživatelích služby Active Directory, nezapomeňte přejít zpět na stránku **adresářové služby** a provést ruční synchronizaci, aby informace v TMWS zůstaly aktuální.

    e. Vyberte **Test připojení** , abyste zkontrolovali, jestli se služba Azure AD může úspěšně připojit.
    
    f. Vyberte **Uložit**.
 
 > [!NOTE]
 > Další informace o tom, jak nakonfigurovat TMWS pomocí Azure AD, najdete v tématu [Konfigurace nastavení Azure AD na TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Test SSO 

Až nakonfigurujete službu Azure AD a jako metodu ověření uživatele zadáte Azure AD, můžete se přihlásit k proxy server TMWS a ověřit instalaci. Když přihlášení Azure AD ověří váš účet, můžete navštívit Internet.

> [!NOTE]
> TMWS nepodporuje testování jednotného přihlašování z portálu Azure AD. v části **Přehled**  >  **jednotného přihlašování**  >  **se nastavuje jednotné přihlašování s**  >  **testem** SAML vaší nové podnikové aplikace.

1. Zrušte zaškrtnutí políček prohlížeče všech souborů cookie a pak restartujte prohlížeč. 

1. Nasměrujte svůj prohlížeč na TMWS proxy server. Podrobnosti najdete v tématu [předávání provozu pomocí souborů PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Navštivte libovolný internetový web. TMWS vás přesměruje na portál TMWS pro všechny uživatele.

1. Zadejte účet Active Directory (formát: *doména* \\ *sAMAccountName* nebo doména *sAMAccountName* @ ), e-mailovou adresu nebo hlavní název uživatele (UPN) a pak vyberte **Přihlásit** se. TMWS vám pošle přihlašovací okno služby Azure AD.

1. V okně přihlášení k Azure AD zadejte svoje přihlašovací údaje k účtu Azure AD. Nyní byste měli být přihlášeni do TMWS.

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy k integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Trend Micro Web Security s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Trend Micro Web Security pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)