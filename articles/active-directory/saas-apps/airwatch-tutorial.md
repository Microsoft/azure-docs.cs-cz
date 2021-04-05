---
title: 'Kurz: Integrace Azure Active Directory se sledováním Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a sledováním.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 4955062e6f0d0c231d09964c985df12284e3733c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653282"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Kurz: integrace programu pro sledování ve službě Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat sledování pomocí Azure Active Directory (Azure AD). Když integrujete službu pro sledování v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke sledování.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke kurzu sledování pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s jednotným přihlašováním (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

* Pro jednotné přihlašování (SSO) **bylo spuštěno** sledování.

## <a name="add-airwatch-from-the-gallery"></a>Přidání sledování z Galerie

Pokud chcete nakonfigurovat integraci programu pro sledování videa do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat sledování z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **prowatch** .
1. Na panelu výsledků vyberte **sledování** videa a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Konfigurace a testování jednotného přihlašování Azure AD pro sledování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím zkušebního uživatele pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci sledování.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí sledování, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro sledování](#configure-airwatch-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele s hodinkami](#create-airwatch-test-user)** , abyste měli protějšek B. Simon ve službě sledování, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace ve službě **Prokukátka** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Do textového pole **identifikátor (ID entity)** zadejte hodnotu jako: `AirWatch`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory](https://www.air-watch.com/company/contact-us/) pro služby sledování. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace pro sledování a očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

1. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name |  Zdrojový atribut|
    |---------------|----------------|
    | UID | User. userPrincipalName |
    | | |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si metadata XML a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení sledování** vzdálení zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke sledování.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **sledování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-airwatch-sso"></a>Nakonfigurovat jednotné přihlašování pro sledování

1. V jiném okně webového prohlížeče se přihlaste k firemnímu webu ve službě prowatch jako správce.

1. Na stránce nastavení. Vyberte **nastavení > Podniková integrace > adresářových služeb**.

   ![Nastavení](./media/airwatch-tutorial/services.png "Nastavení")

1. Klikněte na kartu **uživatel** , do textového pole **Základní rozlišující** název zadejte název domény a pak klikněte na **Uložit**.

   ![Snímek obrazovky, který zvýrazní základní textové pole s rozlišujícím názvem.](./media/airwatch-tutorial/user.png "User")

1. Klikněte na kartu **Server** .

   ![Server](./media/airwatch-tutorial/directory.png "Server")

1. V části **LDAP** proveďte následující kroky:

    ![Snímek obrazovky zobrazující změny, které je třeba provést v části LDAP](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. Jako **Typ adresáře** vyberte **None (žádné**).

    b. **Pro ověřování vyberte použít SAML**.

1. V části **SAML 2,0** nahrajte stažený certifikát kliknutím na **Odeslat**.

    ![Nahrát](./media/airwatch-tutorial/uploads.png "Nahrávání")

1. V části **žádost** proveďte následující kroky:

    ![Oddíl žádosti](./media/airwatch-tutorial/request.png "Žádost")  

    a. Jako **typ vazby žádosti** vyberte **post (Odeslat**).

    b. V Azure Portal na stránce **Konfigurovat jednotné přihlašování** na webu pro sledování videa Zkopírujte hodnotu **Adresa URL pro přihlášení** a vložte ji do textového pole **URL jednotného přihlašování zprostředkovatele identity** .

    c. Jako **Formát NameId** vyberte **e-mailová adresa**.

    d. Jako **zabezpečení žádosti o ověření** vyberte **žádné**.

    e. Klikněte na **Uložit**.

1. Znovu klikněte na kartu **uživatel** .

    ![Uživatel](./media/airwatch-tutorial/users.png "User")

1. V části **atributu** proveďte následující kroky:

    ![Atribut](./media/airwatch-tutorial/attributes.png "Atribut")

    a. Do textového pole **identifikátor objektu** zadejte `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. Do textového pole **uživatelské jméno** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. Do textového pole **Zobrazovaný název** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. Do textového pole **jméno v prvním názvu** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. Do textového pole **příjmení** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Do textového pole **e-mail** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    například Klikněte na **Uložit**.

### <a name="create-airwatch-test-user"></a>Vytvořit testovacího uživatele pro sledování

Aby se uživatelé Azure AD mohli přihlašovat ke službě vzdálení, musí se zřídit ke sledování. V případě vzkrokování je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti s **hodinkami** ve službě providee jako správce.

2. V navigačním podokně na levé straně klikněte na **účty** a potom klikněte na **Uživatelé**.
  
   ![Uživatelé](./media/airwatch-tutorial/accounts.png "Uživatelé")

3. V nabídce **Uživatelé** klikněte na **zobrazení seznamu** a potom klikněte na **Přidat > přidat uživatele**.
  
   ![Snímek obrazovky, který zvýrazní tlačítka Přidat a přidat uživatele](./media/airwatch-tutorial/add.png "Přidání uživatele")

4. V dialogu **Přidat/upravit uživatele** proveďte následující kroky:

   ![Přidat uživatele](./media/airwatch-tutorial/save.png "Přidání uživatele")

   a. Zadejte **uživatelské jméno**, **heslo**, **Potvrdit heslo**, jméno, **příjmení**, **e-mailovou adresu** platného Azure Active Directory **účtu, který** chcete zřídit do souvisejících textových polí.

   b. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API ve službě Vision Watch.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke sledování toku, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení ke sledování toku přímo a spustit tok přihlášení

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici pro sledování videa v části Moje aplikace, přesměruje se na přihlašovací adresu URL pro sledování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile provedete konfiguraci sledování, můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).