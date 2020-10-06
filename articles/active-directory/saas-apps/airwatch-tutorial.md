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
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 2baff2ac2af10caf857fd08b0d7e7df5030220e4
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758128"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Kurz: integrace programu pro sledování ve službě Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat sledování pomocí Azure Active Directory (Azure AD). Když integrujete službu pro sledování v Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke sledování.
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke kurzu sledování pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Pro jednotné přihlašování (SSO) **bylo spuštěno** sledování.

## <a name="adding-airwatch-from-the-gallery"></a>Přidání sledování z Galerie

Pokud chcete nakonfigurovat integraci programu pro sledování videa do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat sledování z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **prowatch** .
1. Na panelu výsledků vyberte **sledování** videa a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím zkušebního uživatele pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci sledování.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím služby sledování, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro sledování](#configure-airwatch-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Vytvořte testovacího uživatele s hodinkami](#create-airwatch-test-user)** , abyste měli protiBrittaho Simonu ve službě sledování, která je propojená s reprezentací uživatele v Azure AD.
5. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace ve službě **prokukátka** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Do textového pole **identifikátor (ID entity)** zadejte hodnotu jako: `AirWatch`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory](https://www.air-watch.com/company/contact-us/) pro služby sledování. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace pro sledování a očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

1. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Název |  Zdrojový atribut|
    |---------------|----------------|
    | UID | User. userPrincipalName |
    | | |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte si metadata XML a uložte je do počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení sledování** vzdálení zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Nakonfigurovat jednotné přihlašování pro sledování

1. V jiném okně webového prohlížeče se přihlaste k firemnímu webu ve službě prowatch jako správce.

1. Na stránce nastavení. Vyberte **nastavení > Podniková integrace > adresářových služeb**.

   ![Nastavení](./media/airwatch-tutorial/ic791921.png "Nastavení")

1. Klikněte na kartu **uživatel** , do textového pole **Základní rozlišující** název zadejte název domény a pak klikněte na **Uložit**.

   ![Snímek obrazovky, který zvýrazní základní textové pole s rozlišujícím názvem.](./media/airwatch-tutorial/ic791922.png "Uživatel")

1. Klikněte na kartu **Server** .

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. V části **LDAP** proveďte následující kroky:

    ![Snímek obrazovky zobrazující změny, které je třeba provést v části LDAP](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Jako **Typ adresáře**vyberte **None (žádné**).

    b. **Pro ověřování vyberte použít SAML**.

1. V části **SAML 2,0** nahrajte stažený certifikát kliknutím na **Odeslat**.

    ![Nahrát](./media/airwatch-tutorial/ic791932.png "Nahrávání")

1. V části **žádost** proveďte následující kroky:

    ![Žádost](./media/airwatch-tutorial/ic791925.png "Žádost")  

    a. Jako **typ vazby žádosti**vyberte **post (Odeslat**).

    b. V Azure Portal na stránce **Konfigurovat jednotné přihlašování** na webu pro sledování videa Zkopírujte hodnotu **Adresa URL pro přihlášení** a vložte ji do textového pole **URL jednotného přihlašování zprostředkovatele identity** .

    c. Jako **Formát NameId**vyberte **e-mailová adresa**.

    d. Jako **zabezpečení žádosti o ověření**vyberte **žádné**.

    e. Klikněte na **Uložit**.

1. Znovu klikněte na kartu **uživatel** .

    ![Uživatel](./media/airwatch-tutorial/ic791926.png "Uživatel")

1. V části **atributu** proveďte následující kroky:

    ![Atribut](./media/airwatch-tutorial/ic791927.png "Atribut")

    a. Do textového pole **identifikátor objektu** zadejte `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. Do textového pole **uživatelské jméno** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. Do textového pole **Zobrazovaný název** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. Do textového pole **jméno v prvním názvu** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. Do textového pole **příjmení** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Do textového pole **e-mail** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    například Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke sledování.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **sledování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-airwatch-test-user"></a>Vytvořit testovacího uživatele pro sledování

Aby se uživatelé Azure AD mohli přihlašovat ke službě vzdálení, musí se zřídit ke sledování. V případě vzkrokování je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti s **hodinkami** ve službě providee jako správce.

2. V navigačním podokně na levé straně klikněte na **účty**a potom klikněte na **Uživatelé**.
  
   ![Uživatelé](./media/airwatch-tutorial/ic791929.png "Uživatelé")

3. V nabídce **Uživatelé** klikněte na **zobrazení seznamu**a potom klikněte na **Přidat > přidat uživatele**.
  
   ![Snímek obrazovky, který zvýrazní tlačítka Přidat a přidat uživatele](./media/airwatch-tutorial/ic791930.png "Přidání uživatele")

4. V dialogu **Přidat/upravit uživatele** proveďte následující kroky:

   ![Přidat uživatele](./media/airwatch-tutorial/ic791931.png "Přidání uživatele")

   a. Zadejte **uživatelské jméno**, **heslo**, **Potvrdit heslo**, jméno, **příjmení**, **e-mailovou adresu** platného Azure Active Directory **účtu, který**chcete zřídit do souvisejících textových polí.

   b. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API ve službě Vision Watch.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu kliknete na dlaždici kolektoru, měli byste být automaticky přihlášení ke sledování, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
