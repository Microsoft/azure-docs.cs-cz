---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Workday | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: de7c1d037ce19f591829c340282facbd70a7258a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k Workday

V tomto kurzu se naučíte, jak integrovat Workday s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete Workday, můžete:

* Řízení ve službě Azure AD, která má přístup k Workday.
* Umožněte uživatelům, aby se do Workday přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povolený jednotného přihlašování (SSO) pro Workday.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Workday podporuje jednotné přihlašování iniciované v **SP** .

* Po nakonfigurování Workday můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Přidání pracovního dne z Galerie

Pokud chcete nakonfigurovat integraci Workday do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Workday z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Workday** .
1. Vyberte pracovní **den** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Workday

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Workday pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Workday.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Workday, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
2. **[Nakonfigurujte Workday](#configure-workday)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele v Workday](#create-workday-test-user)** , který bude mít protějšek B. Simon v Workday, který je propojený s reprezentací uživatele Azure AD.
3. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Workday** najděte část **Správa** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `http://www.workday.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a adresy URL odpovědi. Vaše adresa URL odpovědi musí mít subdoménu například: www, WD2, WD3, WD3-impl, wd5, wd5-impl).
    > Použití něčeho jako `http://www.myworkday.com` funguje `http://myworkday.com` , ale ne. Pro získání těchto hodnot se obraťte na [tým podpory klienta v Workday](https://www.workday.com/en-us/partners-services/services/support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Vaše aplikace Workday očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName** . Aplikace Workday očekává, že **NameIdentifier** má být namapován pomocí **User. mail** , **UPN** atd., takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

    > [!NOTE]
    > Tady jsme namapovali ID s názvem UPN (User. userPrincipalName) jako výchozí. Abyste mohli úspěšně pracovat s jednotným přihlašováním, je potřeba, abyste namapovali ID, které má skutečné ID uživatele v účtu Workday (e-mail, hlavní název uživatele atd.).

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. Chcete-li upravit možnosti **podepisování** podle vašich požadavků, klikněte na tlačítko **Upravit** a otevřete dialogové okno **podpisový certifikát SAML** .

    ![Snímek obrazovky se stránkou podpisového certifikátu SAML se zvolenou ikonou pro úpravy.](common/edit-certificate.png) 

    ![Snímek obrazovky se zobrazí stránka podpisový certifikát SAML, kde můžete vybrat možnost podepisování.](./media/workday-tutorial/signing-option.png)

    a. Pro **možnost podepisování** vyberte **podepsat odpověď SAML a kontrolní výraz** .

    b. Klikněte na **Uložit** .

1. V části **nastavit pracovní den** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Workday.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. V seznamu aplikace vyberte **Workday** .
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny** .

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-workday"></a>Konfigurace pracovního dne

1. V jiném okně webového prohlížeče se přihlaste ke svému firemnímu webu Workday jako správce.

2. Do **vyhledávacího pole** vyhledejte název **Upravit nastavení klienta – zabezpečení** v levé horní části domovské stránky.

    ![Upravit zabezpečení tenanta](./media/workday-tutorial/IC782925.png "Upravit zabezpečení tenanta")

3. V části **adresy URL pro přesměrování** proveďte následující kroky:

    ![Adresy URL pro přesměrování](./media/workday-tutorial/IC7829581.png "Adresy URL pro přesměrování")

    a. Klikněte na tlačítko **Přidat řádek** .

    b. Do textového pole **Adresa URL pro přesměrování přihlášení** , **Adresa URL pro přesměrování časového limitu** a text **adresy URL pro přesměrování mobilního přesměrování** vložte **adresu URL pro přihlášení** , kterou jste zkopírovali z části **Nastavení pracovního** dne v Azure Portal.

    c. Do textového pole **Adresa URL pro přesměrování odhlášení** vložte **adresu URL pro odhlášení** , kterou jste zkopírovali z části **Nastavení pracovního** dne v Azure Portal.

    d. V části **použito pro prostředí** pole vyberte název prostředí.  

   > [!NOTE]
   > Hodnota atributu prostředí je vázána na hodnotu adresy URL klienta:  
   > – Pokud název domény adresy URL tenanta Workday začíná na impl, například *https://www.myworkday.com/ tenant nebo login-saml2.htmld* ), musí být atribut **prostředí** nastavený na implementaci.  
   > – Pokud název domény začíná na něco jiného, musíte kontaktovat [tým podpory pro pracovníka pracovního](https://www.workday.com/en-us/partners-services/services/support.html) dne a získat tak porovnání hodnoty **prostředí** .

4. V části **Nastavení SAML** proveďte následující kroky:

    ![Nastavení SAML](./media/workday-tutorial/IC782926.png "Nastavení SAML")

    a.  Vyberte **Povolit ověřování SAML** .

    b.  Klikněte na tlačítko **Přidat řádek** .

5. V části **Zprostředkovatelé identity SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce zprostředkovatelé identit SAML, kde můžete provádět tyto kroky.](./media/workday-tutorial/IC7829271.png "Zprostředkovatelé identit SAML")

    a. Do textového pole **název zprostředkovatele identity** zadejte název zprostředkovatele (například: *SPInitiatedSSO* ).

    b. V Azure Portal v části nastavit pracovní **den** Zkopírujte hodnotu **identifikátoru Azure AD** a vložte ji do textového pole **vystavitele** .

    ![Snímek obrazovky ukazuje, kde můžete zadat hodnotu vystavitele.](./media/workday-tutorial/IC7829272.png "Zprostředkovatelé identit SAML")

    c. V Azure Portal v části nastavit pracovní **den** ZKOPÍRUJTE hodnotu **URL pro odhlášení** a vložte ji do textového pole **Adresa URL odpovědi na odhlášení** .

    d. V Azure Portal v části nastavit pracovní **den** Zkopírujte hodnotu **adresy URL pro přihlášení** a vložte ji do textového pole **URL služby IDP SSO** .

    e. V části **použito pro prostředí** pole vyberte název prostředí.

    f. Klikněte na **certifikát veřejného klíče zprostředkovatele identity** a pak klikněte na **vytvořit** .

    ![Snímek obrazovky se zobrazí v odkazu vytvořit.](./media/workday-tutorial/IC782928.png "Vytvořit")

    například Klikněte na **vytvořit veřejný klíč x509** .

    ![Snímek obrazovky ukazuje možnost vytvořit veřejný klíč x509.](./media/workday-tutorial/IC782929.png "Vytvořit")

6. V části **Zobrazit veřejný klíč x509** proveďte následující kroky:

    ![Zobrazit veřejný klíč x509](./media/workday-tutorial/IC782930.png "Zobrazit veřejný klíč x509")

    a. Do textového pole **název** zadejte název vašeho certifikátu (například: *OOP \_ SP* ).

    b. Do textového pole **platné od** zadejte platnou hodnotu atributu certifikátu.

    c.  Do textového pole **platné do** zadejte hodnotu atributu platný do atributu vašeho certifikátu.

    > [!NOTE]
    > Poklikáním na něj můžete získat platné datum a čas platnosti ze staženého certifikátu.  Data jsou uvedena na kartě **Podrobnosti** .
    >
    >

    d.  Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 a zkopírujte jeho obsah.

    e.  Do textového pole **certifikát** vložte obsah schránky.

    f.  Klikněte na **OK** .

7. Proveďte tyto kroky:

    ![Konfigurace jednotného přihlašování](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Konfigurace jednotného přihlašování")

    a.  Do textového pole **ID poskytovatele služby** zadejte **http://www.workday.com** .

    b. Vyberte **Neuprostřed žádosti o ověření iniciované v SP** .

    c. Jako **metodu podpisu žádosti o ověření** vyberte **SHA256** .

    ![Metoda podpisu žádosti o ověření](./media/workday-tutorial/WorkdaySSOConfiguration.png "Metoda podpisu žádosti o ověření")

    d. Klikněte na **OK** .

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Ujistěte se prosím, že jste správně nastavili jednotné přihlašování. Pokud povolíte jednotné přihlašování s nesprávným nastavením, možná nebudete moct aplikaci zadat pomocí vašich přihlašovacích údajů a můžete ji uzamknout. V takovém případě Workday poskytuje adresu URL pro zálohování, kde se uživatelé můžou přihlašovat pomocí normálního uživatelského jména a hesla v následujícím formátu: [vaše adresa URL pracovního dne]/Login.Flex? redirect = n

### <a name="create-workday-test-user"></a>Vytvořit testovacího uživatele v Workday

V této části vytvoříte v Workday uživatele s názvem B. Simon. Pokud chcete přidat uživatele do platformy Workday, pracujte s [týmem podpory klientů v pracovních klientech](https://www.workday.com/en-us/partners-services/services/support.html) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Workday na přístupovém panelu, měli byste být automaticky přihlášení do pracovního dne, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Workday ve službě Azure AD](https://aad.portal.azure.com)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Workday pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/protect-workday)