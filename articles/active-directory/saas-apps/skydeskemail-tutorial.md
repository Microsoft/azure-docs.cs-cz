---
title: 'Kurz: Azure Active Directory integrace s SkyDesk e-mailem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SkyDesk e-mailem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516016"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Kurz: Azure Active Directory integrace s SkyDesk e-mailem

V tomto kurzu se dozvíte, jak integrovat SkyDesk E-mail s Azure Active Directory (Azure AD).
Integrace SkyDeskho e-mailu s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k e-mailu SkyDesk.
* Uživatelům můžete povolit, aby se automaticky přihlásili k SkyDesk e-mailu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s SkyDesk e-mailem, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* SkyDesk e-mailové předplatné s povoleným jediným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SkyDesk E-mail podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-skydesk-email-from-the-gallery"></a>Přidání e-mailu SkyDesk z Galerie

Pokud chcete nakonfigurovat integraci SkyDesk e-mailu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat SkyDesk email z galerie.

**K přidání e-mailu SkyDesk z Galerie proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SkyDesk e-mail**, vyberte **SkyDesk email** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![SkyDesk e-maily v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s SkyDesk e-mailem na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SkyDesk e-mailu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí e-mailu SkyDesk, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování SkyDesk e-mailem](#configure-skydesk-email-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření SkyDesk e-mailového testu uživatele](#create-skydesk-email-test-user)** – Pokud chcete mít protějšek Britta Simon v SkyDesk e-mailu, který se odkazuje na reprezentaci uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí e-mailu SkyDesk, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **e-mailových aplikací SkyDesk** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování SkyDesk e-mailové domény a adresy URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory SkyDesk e-mailových klientů](https://www.skydesk.jp/apps/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení e-mailu SkyDesk** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurovat SkyDesk e-mailem na jednom Sign-On

1. V jiném webovém prohlížeči se přihlaste ke svému e-mailovému účtu SkyDesk jako správce.

1. V nabídce v horní části klikněte na **Nastavení** a vyberte **org**.

    ![Snímek obrazovky se zobrazí v nabídce nastavení vybraná možnost org.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. V levém panelu klikněte na **domény** .

    ![Snímek obrazovky zobrazuje domény vybrané v Ovládacích panelech.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klikněte na **Přidat doménu**.

    ![Snímek obrazovky znázorňující vybranou možnost Přidat doménu.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Zadejte název domény a pak ověřte doménu.

    ![Snímek obrazovky se zobrazí karta přidat doménu, kde můžete zadat doménu.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Na levém panelu klikněte na **ověřování SAML** .

    ![Snímek obrazovky ukazuje ověřování SAML vybrané z ovládacích panelů.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na stránce **ověřovací** dialog pro SAML proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v dialogovém okně Podrobnosti ověřování SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Pokud chcete používat ověřování na základě SAML, měli byste buď ověřit nastavení **adresy URL** v **doméně** nebo portálu. Můžete nastavit adresu URL portálu jedinečným názvem.

    ![Snímek obrazovky ukazuje portál U R L, kde zadáte název.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole Adresa URL pro **odhlášení** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    c. **Změna adresy URL hesla** je volitelná, takže zůstane prázdná.

    d. Kliknutím na **získat klíč ze souboru** vyberte stažený certifikát z Azure Portal a potom kliknutím na **otevřít** Nahrajte certifikát.

    e. Jako **algoritmus** vyberte **RSA**.

    f. Změny uložíte kliknutím na tlačítko **OK** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k e-mailu SkyDesk.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **SkyDesk e-mail**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SkyDesk e-mail**.

    ![SkyDesk e-mailový odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-skydesk-email-test-user"></a>Vytvořit uživatele testovacího e-mailu SkyDesk

V této části vytvoříte v e-mailu SkyDesk uživatele s názvem Britta Simon.

V SkyDesk e-mailu klikněte na **přístup uživatelů** na levém panelu a pak zadejte svoje uživatelské jméno.

![Snímek obrazovky zobrazuje uživatelský přístup vybraný z ovládacích panelů.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Pokud potřebujete vytvořit hromadné uživatele, musíte se obrátit na [tým podpory e-mailových klientů SkyDesk](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SkyDesk E-mail, měli byste se automaticky přihlásit k e-mailu SkyDesk, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)