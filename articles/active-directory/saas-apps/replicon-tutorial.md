---
title: 'Kurz: Azure Active Directory integrace s Replicon | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688497"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Kurz: integrace Replicon s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Replicon s Azure Active Directory (Azure AD). Když integrujete Replicon s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Replicon.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Replicon svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Replicon odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Replicon podporuje jednotné přihlašování iniciované v **SP** .

## <a name="adding-replicon-from-the-gallery"></a>Přidání Replicon z Galerie

Pokud chcete nakonfigurovat integraci Replicon do služby Azure AD, musíte přidat Replicon z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Replicon** .
1. Na panelu výsledků vyberte **Replicon** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Replicon pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Replicon.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Replicon, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte REPLICON SSO](#configure-replicon-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte Replicon Test User](#create-replicon-test-user)** -to, abyste měli protějšek B. Simon v Replicon, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Replicon** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://global.replicon.com/!/saml2/<client name>`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Replicon](https://www.replicon.com/customerzone/contact-support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Upravte nastavení kliknutím na ikonu Upravit/pero pro **podpisový certifikát SAML** .

    ![Podpisový algoritmus](common/signing-algorithm.png)

    1. Jako **možnost podepisování** vyberte **podepsat kontrolní výraz SAML** .

    1. Jako **podpisový algoritmus** vyberte **SHA-256** .

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurace jednotného přihlašování Replicon

1. V jiném okně webového prohlížeče se přihlaste k webu Replicon společnosti jako správce.

2. Ke konfiguraci SAML 2,0 proveďte následující kroky:

    ![Povolit ověřování SAML](./media/replicon-tutorial/ic777805.png "Povolit ověřování SAML")

    a. Chcete-li zobrazit dialogové okno **EnableSAML Authentication2** , přidejte následující adresu URL za klíč vaší společnosti: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Následuje příklad schématu pro úplnou adresu URL: `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kliknutím na **+** rozbalte část **v20Configuration** .

   c. Kliknutím na **+** rozbalte část **metaDataConfiguration** .

   d. Vyberte **SHA256** pro xmlSignatureAlgorithm

   e. Klikněte na **zvolit soubor**, vyberte soubor XML s metadaty poskytovatele identity a klikněte na **Odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Replicon.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Replicon**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-replicon-test-user"></a>Vytvořit testovacího uživatele Replicon

Cílem této části je vytvořit uživatele s názvem B. Simon v Replicon.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. V okně webového prohlížeče se přihlaste k webu Replicon společnosti jako správce.

2. Přejít na **\> uživatele pro správu**.

    ![Uživatelé](./media/replicon-tutorial/ic777806.png "Uživatelé")

3. Klikněte na **+ Přidat uživatele**.

    ![Přidat uživatele](./media/replicon-tutorial/ic777807.png "Přidání uživatele")

4. V části **Profil uživatele** proveďte následující kroky:

    ![Profil uživatele](./media/replicon-tutorial/ic777808.png "Profil uživatele")

    a. Do textového pole **přihlašovací jméno** zadejte e-mailovou adresu Azure AD pro uživatele Azure AD, kterého chcete zřídit `B.Simon@contoso.com` .

    > [!NOTE]
    > Přihlašovací jméno musí odpovídat e-mailové adrese uživatele ve službě Azure AD.

    b. Jako **typ ověřování** vyberte **jednotné přihlašování**.

    c. Nastavte ID ověřování na stejnou hodnotu jako přihlašovací jméno (e-mailová adresa Azure AD uživatele).

    d. Do textového pole **oddělení** zadejte oddělení uživatele.

    e. Jako **typ zaměstnance** vyberte **správce**.

    f. Klikněte na **Uložit profil uživatele**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Replicon nebo rozhraní API poskytovaná Replicon.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Replicon, měli byste se automaticky přihlásit k Replicon, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)