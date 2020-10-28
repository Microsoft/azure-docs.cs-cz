---
title: 'Kurz: Azure Active Directory integrace se SAP Business ByDesign | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 91c0eaf5b1e614e185747cd9ae2c32e7f4f7a707
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672069"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign

V tomto kurzu se dozvíte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD).
Integrace SAP Business ByDesign s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k SAP Business ByDesign.
* Uživatelům můžete povolit, aby se automaticky přihlásili k SAP Business ByDesign (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s SAP Business ByDesign potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné SAP Business ByDesign s podporou jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Business ByDesign podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z Galerie

Pokud chcete nakonfigurovat integraci SAP Business ByDesign do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SAP Business ByDesign z galerie.

**Pokud chcete přidat SAP Business ByDesign z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Business ByDesign** , vyberte **SAP Business ByDesign** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SAP Business ByDesign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SAP Business ByDesign na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP Business ByDesign.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP Business ByDesign, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele SAP Business ByDesign](#create-sap-business-bydesign-test-user)** , abyste měli protějšek Britta Simon v SAP Business ByDesign, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SAP Business ByDesign, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **SAP Business ByDesign** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně SAP Business ByDesign a adresám URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.sapbydesign.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory pro klienta SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Aplikace SAP Business ByDesign očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. Kliknutím na ikonu **Upravit** upravíte **hodnotu identifikátoru názvu** .

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. V části **Spravovat deklarace identity uživatelů** proveďte následující kroky:  ![ Obrázek](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Jako **zdroj** vyberte **transformovat** .

    b. V rozevíracím seznamu **transformace** vyberte **ExtractMailPrefix ()** .

    c. V rozevíracím seznamu **parametr 1** vyberte atribut uživatele, který chcete použít pro vaši implementaci. Například pokud chcete použít ČísloZaměstnance jako jedinečný identifikátor uživatele a uloženou hodnotu atributu v ExtensionAttribute2, vyberte User. ExtensionAttribute2.

    d. Klikněte na **Uložit** .

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **nastavení SAP Business ByDesign** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurace SAP Business ByDesign Single Sign-On

1. Přihlaste se k portálu SAP Business ByDesign s právy správce.

2. Přejděte na **běžný úkol správy aplikací a uživatelů** a klikněte na kartu **poskytovatel identity** .

3. Klikněte na **Nový zprostředkovatel identity** a vyberte soubor XML s metadaty, který jste stáhli z Azure Portal. Po importu metadat systém automaticky odešle požadovaný podpisový certifikát a šifrovací certifikát.

    ![Konfigurace jednoho Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Pokud chcete do žádosti SAML zahrnout **adresu URL služby pro příjemce kontrolního výrazu** , vyberte **zahrnout adresu URL služby pro příjemce kontrolního výrazu** .

5. Klikněte na **aktivovat jednotné přihlašování** .

6. Uložte provedené změny.

7. Klikněte na kartu **můj systém** .

    ![Konfigurace jednoho Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. V poli **přihlašovací adresa URL pro přihlášení k Azure AD** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    ![Konfigurace jednoho Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Určete, jestli si zaměstnanec může ručně vybrat přihlášení pomocí ID uživatele a hesla nebo jednotného přihlašování. vybere **Výběr ručního zprostředkovatele identity** .

10. V části **Adresa URL jednotného přihlašování** zadejte adresu URL, kterou by měl zaměstnanec použít k jednotnéhoí systému.
    V rozevíracím seznamu Adresa URL odeslání na zaměstnanec si můžete vybrat z následujících možností:

    **Adresa URL bez jednotného přihlašování**

    Systém pošle zaměstnanci pouze normální adresu URL systému. Zaměstnanec se nemůže jednotného pomocí jednotného přihlašování a musí místo toho použít heslo nebo certifikát.

    **ADRESA URL JEDNOTNÉHO PŘIHLAŠOVÁNÍ**

    Systém pošle zaměstnanci pouze adresu URL jednotného přihlašování. Zaměstnanec může jednotného pomocí jednotného přihlašování. Požadavek na ověření se přesměruje přes IdP.

    **Automatický výběr**

    Pokud jednotné přihlašování není aktivní, pošle se zaměstnanci obvyklá systémová adresa URL. Pokud je jednotné přihlašování aktivní, systém zkontroluje, jestli má zaměstnanec heslo. Pokud je k dispozici heslo, adresa URL jednotného přihlašování a adresa URL bez jednotného přihlašování se odešlou zaměstnanci. Pokud ale nemá zaměstnanec žádné heslo, pošle se zaměstnanci jenom adresa URL jednotného přihlašování.

11. Uložte provedené změny.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k SAP Business ByDesign.

1. V Azure Portal vyberte možnost **podnikové aplikace** , vyberte možnost **všechny aplikace** a pak vyberte **SAP Business ByDesign** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SAP Business ByDesign** .

    ![Odkaz SAP Business ByDesign v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sap-business-bydesign-test-user"></a>Vytvořit testovacího uživatele SAP Business ByDesign

V této části vytvoříte uživatele s názvem Britta Simon v SAP Business ByDesign. Pokud chcete přidat uživatele na platformě SAP Business ByDesign, obraťte se na [tým podpory klientů SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . 

> [!NOTE]
> Ujistěte se prosím, že hodnota NameID by se měla shodovat s polem username v platformě SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAP Business ByDesign na přístupovém panelu byste měli být automaticky přihlášení k SAP Business ByDesign, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)