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
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: b7ee726c9a5501235123a393d144c56a0342a5ee
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k Workday

V tomto kurzu se naučíte, jak integrovat Workday s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete Workday, můžete:

* Řízení ve službě Azure AD, která má přístup k Workday.
* Umožněte uživatelům, aby se do Workday přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povolený jednotného přihlašování (SSO) pro Workday.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Workday podporuje jednotné přihlašování iniciované v **SP** .

* Mobilní aplikace Workday se teď dá nakonfigurovat se službou Azure AD pro povolení jednotného přihlašování. Další podrobnosti o tom, jak nakonfigurovat, najdete na [tomto](workday-mobile-tutorial.md) odkazu.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-workday-from-the-gallery"></a>Přidání pracovního dne z Galerie

Pokud chcete nakonfigurovat integraci Workday do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Workday z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace** .
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace** .
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Workday** .
1. Vyberte pracovní **den** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Workday

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s Workday pomocí testovacího uživatele s názvem **B. Simon** . Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Workday.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Workday, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
2. **[Nakonfigurujte Workday](#configure-workday)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele v Workday](#create-workday-test-user)** , který bude mít protějšek B. Simon v Workday, který je propojený s reprezentací uživatele Azure AD.
3. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Workday** najděte část **Správa** a vyberte **jednotné přihlašování** .
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** .
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Do textového pole **odhlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, adresy URL odpovědi a adresy URL pro odhlášení. Vaše adresa URL odpovědi musí mít subdoménu například: www, WD2, WD3, WD3-impl, wd5, wd5-impl).
    > Použití něčeho jako `http://www.myworkday.com` funguje `http://myworkday.com` , ale ne. Pro získání těchto hodnot se obraťte na [tým podpory klienta v Workday](https://www.workday.com/en-us/partners-services/services/support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Vaše aplikace Workday očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName** . Aplikace Workday očekává, že **NameIdentifier** má být namapován pomocí **User. mail** , **UPN** atd., takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

    > [!NOTE]
    > Tady jsme namapovali ID s názvem UPN (User. userPrincipalName) jako výchozí. Abyste mohli úspěšně pracovat s jednotným přihlašováním, je potřeba, abyste namapovali ID, které má skutečné ID uživatele v účtu Workday (e-mail, hlavní název uživatele atd.).

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. Chcete-li upravit možnosti **podepisování** podle vašich požadavků, klikněte na tlačítko **Upravit** a otevřete dialogové okno **podpisový certifikát SAML** .

    ![Certifikát](common/edit-certificate.png) 

    ![Podpisový certifikát SAML](./media/workday-tutorial/signing-option.png)

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
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci nebyla nastavena žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-workday"></a>Konfigurace pracovního dne

1. V jiném okně webového prohlížeče se přihlaste ke svému firemnímu webu Workday jako správce.

1. Do **vyhledávacího pole** vyhledejte název **Upravit nastavení klienta – zabezpečení** v levé horní části domovské stránky.

    ![Upravit zabezpečení tenanta](./media/workday-tutorial/IC782925.png "Upravit zabezpečení tenanta")


1. V části **Nastavení SAML** proveďte následující kroky:

    ![Nastavení SAML](./media/workday-tutorial/IC782926.png "Nastavení SAML")

    a.  Vyberte **Povolit ověřování SAML** .

    b.  Klikněte na tlačítko **Přidat řádek** .

1. V části **Zprostředkovatelé identity SAML** prosím proveďte následující akce pro nově vytvořený řádek.

    a. Pro pole, která jsou zobrazena níže, proveďte následující akce.

    ![Zprostředkovatelé identity SAML 1](./media/workday-tutorial/IC7829271.png "Zprostředkovatelé identit SAML")

    * Do textového pole **název zprostředkovatele identity** zadejte název zprostředkovatele (například: *SPInitiatedSSO* ).

    * V Azure Portal v části nastavit pracovní **den** Zkopírujte hodnotu **identifikátoru Azure AD** a vložte ji do textového pole **vystavitele** .

    * Otevřete stažený **certifikát** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **certifikátu x. 509** .

    b. Pro pole, která jsou zobrazena níže, proveďte následující akce.

    ![Zprostředkovatelé identity SAML 2](./media/workday-tutorial/saml-identity-provider-2.png "Zprostředkovatelé identit SAML")

    * Zaškrtněte políčko **Povolit odhlašovací IDP inicializované** .

    * Do textového pole **Adresa URL odpovědi na odhlášení** zadejte **http://www.workday.com** .

    * Do textového pole **Adresa URL žádosti o odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    * Klikněte na zaškrtávací políčko pro inicializaci v systému **SP** .

    * Do textového pole **ID poskytovatele služby** zadejte **http://www.workday.com** .


    * Vyberte **Neuprostřed žádosti o ověření iniciované v SP** .

    c. Pro pole, která jsou zobrazena níže, proveďte následující akce.

    ![Zprostředkovatelé identity SAML 3](./media/workday-tutorial/saml-identity-provider-3.png "Zprostředkovatelé identit SAML")

    * V Azure Portal v části nastavit pracovní **den** Zkopírujte hodnotu **adresy URL pro přihlášení** a vložte ji do textového pole **URL služby IDP SSO** .

    * V poli **použito pro prostředí** pole vyberte odpovídající názvy prostředí z rozevíracího seznamu.

1. V níže uvedeném obrázku proveďte následující kroky.

    ![Workday](./media/workday-tutorial/service-provider.png "Zprostředkovatelé identit SAML")

    a. Do textového pole **ID poskytovatele služby (bude zastaralé)** zadejte **http://www.workday.com** .

    b. Do textového pole **URL služby IDP SSO (bude zastaralé)** zadejte hodnotu **adresy URL pro přihlášení** .

    c. Vyberte **Neuprostřed žádosti o ověření iniciované v SP (bude se používat jako zastaralé)** .

    d. V případě **metody podpis žádosti o ověření** vyberte **SHA256** .

    e. Klikněte na **OK** .

    > [!NOTE]
    > Ujistěte se prosím, že jste správně nastavili jednotné přihlašování. Pokud povolíte jednotné přihlašování s nesprávným nastavením, možná nebudete moct aplikaci zadat pomocí vašich přihlašovacích údajů a můžete ji uzamknout. V takovém případě Workday poskytuje adresu URL pro zálohování, kde se uživatelé můžou přihlašovat pomocí normálního uživatelského jména a hesla v následujícím formátu: [vaše adresa URL pracovního dne]/Login.Flex? redirect = n

### <a name="create-workday-test-user"></a>Vytvořit testovacího uživatele v Workday

1. Přihlaste se k webu Workday společnosti jako správce.

1. Klikněte na **profil** v pravém horním rohu, vyberte **Domů** a na kartě **aplikace** klikněte na **adresář** . 

1. Na stránce **adresář** vyberte možnost **Najít pracovní procesy** na kartě zobrazení.

    ![Najít pracovní procesy](./media/workday-tutorial/user-directory.png)

1.  Na stránce **Najít pracovní procesy** vyberte uživatele z výsledků.

1. Na následující stránce vyberte **úlohu > zabezpečení pracovního procesu** a **účet Workday** se musí shodovat s Azure Active Directory jako hodnota **ID názvu** .

    ![Zabezpečení pracovního procesu](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Další informace o tom, jak vytvořit testovacího uživatele v pracovní den, získáte od [týmu podpory pracovního](https://www.workday.com/en-us/partners-services/services/support.html)dne.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení do Workday, kde můžete spustit tok přihlášení. 

2. Přejít na adresu URL pro přihlášení k Workday přímo a zahájit tok přihlášení.

3. Můžete použít panel Microsoft Access. Po kliknutí na dlaždici pracovního dne na přístupovém panelu byste měli být automaticky přihlášení do pracovního dne, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Workday můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)
