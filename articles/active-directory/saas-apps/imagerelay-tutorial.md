---
title: 'Kurz: Azure Active Directory integrace s Image Relay | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a předáváním obrázků.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: b76c2e346adb6c2afd146b0d73c8f20165145bac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460355"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Kurz: Azure Active Directory integrace s obrázkovým přenosem

V tomto kurzu se naučíte integrovat službu Image Relay s Azure Active Directory (Azure AD).
Integrace Image Relay se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k předávání obrázků.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Image Relay (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím Image Relay potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr Image Relay s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Image Relay podporuje jednotné přihlašování na webu **SP**

## <a name="adding-image-relay-from-the-gallery"></a>Přidání přenosu obrázků z Galerie

Pokud chcete nakonfigurovat integraci Image Relay do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie Image Relay.

**Chcete-li přidat z Galerie obrázkový přenos, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte text **Relay**, vyberte z panelu výsledek možnost **přenášet obrázkem** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Předávání obrázků v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s použitím Image Relay na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci předávání obrazu.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD s využitím Image Relay, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování přes Image Relay](#configure-image-relay-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele pro Image Relay](#create-image-relay-test-user)** – Chcete-li mít protějšek Britta Simon v obrazovém přenosu, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s využitím Image Relay, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Image Relay** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL Image Relay](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.imagerelay.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot [tým podpory pro klienty](http://support.imagerelay.com/) s kontaktem pro předávání obrázků. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení relé pro Image** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-image-relay-single-sign-on"></a>Konfigurace jednoho Sign-On pro přenos obrázků

1. V jiném okně prohlížeče se přihlaste k webovému serveru Image Relay jako správce.

2. Na panelu nástrojů v horní části klikněte na úlohu **uživatele & oprávnění** .

    ![Snímek obrazovky zobrazuje uživatele & oprávnění vybraná na panelu nástrojů.](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klikněte na **vytvořit nové oprávnění**.

    ![Snímek obrazovky se zobrazí textové pole pro zadání názvu oprávnění a možnost výběru typu oprávnění.](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. V úloze **nastavení jednotného přihlašování** zaškrtněte políčko **Tato skupina se může přihlásit jenom pomocí jednotného přihlašování** a pak klikněte na **Uložit**.

    ![Snímek obrazovky se zobrazí nastavení jednotného přihlašování, kde můžete vybrat možnost.](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Přejít na **Nastavení účtu**.

    ![Snímek obrazovky s možností zobrazení panelu nastavení účtu](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Přejít na úlohu **nastavení jednotného přihlašování** .

    ![Snímek obrazovky s možností jednotného přihlašování zobrazí možnost v nabídce nastavení.](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. V dialogovém okně **Nastavení SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v dialogovém okně nastavení SAML, kde můžete zadat informace.](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro odhlášení**  vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    c. Jako **formát ID názvu** vyberte název **urn: Oasis: název: TC: SAML: 1.1: NameId-Format: EmailAddress**.

    d. Jako **Možnosti vazby pro žádosti od poskytovatele služeb (Image Relay)** vyberte **post Binding (Odeslat vazbu**).

    e. V části **certifikát x. 509** klikněte na **aktualizovat certifikát**.

    ![Snímek obrazovky ukazuje možnost aktualizovat certifikát.](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikát x. 509** .

    ![Snímek obrazovky ukazuje certifikát x. 509.](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    například V části **zřizování uživatelů za běhu** vyberte možnost **Povolit zřizování uživatelů za běhu**.

    ![Snímek obrazovky ukazuje oddíl zřizování uživatelů za běhu s vybraným ovládacím prvkem povolit.](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vyberte skupinu oprávnění (například **SSO Basic**), která se smí přihlásit jenom prostřednictvím jednotného přihlašování.

    ![Snímek obrazovky zobrazuje oddíl zřizování uživatelů za běhu s vybraným s s s s.](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k předávání obrázků.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Image Relay**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **předávání obrázků**.

    ![Odkaz na přenos obrázků v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-image-relay-test-user"></a>Vytvořit testovacího uživatele pro Image Relay

Cílem této části je vytvořit uživatele s názvem Britta Simon v obrazovém přenosu.

**Pokud chcete vytvořit uživatele s názvem Britta Simon v imagi přenosu, proveďte následující kroky:**

1. Přihlaste se k webu Image Relay společnosti jako správce.

2. Klepněte na **uživatelé & oprávnění**     a vyberte **vytvořit uživatele jednotného přihlašování**.

    ![Snímek obrazovky se v nabídce zobrazí možnost vytvořit S/O uživatel.](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Zadejte **e-mail**, jméno, **příjmení** a **firmu** **uživatele, kterého** chcete ZŘÍDIT, a vyberte skupinu oprávnění (například SSO Basic), která je skupina, která se může přihlásit pouze prostřednictvím jednotného přihlašování.

    ![Snímek obrazovky se zobrazí stránka vytvořit uživatele S příponou O/S, kde můžete zadat požadované informace.](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klikněte na **Vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici s obrázkem Relay na přístupovém panelu byste měli být automaticky přihlášeni k předávání obrázků, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)