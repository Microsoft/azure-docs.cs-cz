---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s přiblížením | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přiblížení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d5a87d4723bcc21b75db1b31ada72823abdf02
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s přiblížením

V tomto kurzu se dozvíte, jak integrovat přiblížení pomocí Azure Active Directory (Azure AD). Když provádíte integraci lupy s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k přiblížení
* Umožněte, aby se vaši uživatelé automaticky přihlásili, aby se přiblížili svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přiblíží odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Přiblížení podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-zoom-from-the-gallery"></a>Přidání přiblížení z Galerie

Pokud chcete nakonfigurovat integraci přiblížení do Azure AD, musíte přidat přiblížení z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **přiblížení** .
1. Vyberte možnost **Zvětšit** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Konfigurace a testování jednotného přihlašování Azure AD pro přiblížení

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s přiblížením pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v přiblížení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s přiblížením, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte přihlašování k přiblížení](#configure-zoom-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele](#create-zoom-test-user)** s přiblížením pro přiblížení, abyste měli protějšek B. Simon v přiblížení, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **Lupa** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.zoom.us`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`<companyname>.zoom.us`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro lupu](https://support.zoom.us/hc/en-us) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení přiblížení** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

> [!NOTE]
> Informace o tom, jak nakonfigurovat roli v Azure AD, najdete v tématu [konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

> [!NOTE]
> Přiblížení může očekávat deklaraci skupiny v datové části SAML. Pokud jste vytvořili nějaké skupiny, obraťte se na [tým podpory pro přiblížení klientů](https://support.zoom.us/hc/en-us) s informacemi o skupině, aby mohli na svém konci konfigurovat informace o skupině. Také je nutné zadat ID objektu pro [přiblížení týmu podpory klientů](https://support.zoom.us/hc/en-us) , aby mohli na konci nakonfigurovat ID objektu. Pokud chcete získat ID objektu, přečtěte si téma [Konfigurace přiblížení pomocí Azure](https://support.zoom.us/hc/en-us/articles/115005887566).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
    1. Do pole **Název** zadejte `B.Simon`.  
    1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
    1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k přiblížení.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Lupa**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zoom-sso"></a>Konfigurovat přihlašování k přiblížení

1. V jiném okně webového prohlížeče se přihlaste k webovému serveru lupy jako správce.

2. Klikněte na kartu **jednotného přihlašování** .

    ![Karta jednotného přihlašování](./media/zoom-tutorial/ic784700.png "Jednotné přihlašování")

3. Klikněte na kartu **řízení zabezpečení** a pak přejděte na nastavení **jednotného přihlašování** .

4. V části jednotné přihlašování proveďte následující kroky:

    ![Oddíl jednotného přihlašování](./media/zoom-tutorial/ic784701.png "Jednotné přihlašování")

    a. Do textového pole **Adresa URL přihlašovací stránky** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. V případě hodnoty **URL stránky** pro odhlášení musíte přejít na Azure Portal a kliknout na **Azure Active Directory** na levé straně a přejít na **Registrace aplikací**.

    ![Tlačítko Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Klikněte na **koncové body** .

    ![Tlačítko pro koncový bod](./media/zoom-tutorial/endpoint.png)

    d. Zkopírujte **koncový bod odhlašování SAML-P** a vložte ho do textového pole **Adresa URL stránky pro** odhlášení.

    ![Tlačítko pro kopírování koncového bodu](./media/zoom-tutorial/endpoint1.png)

    e. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .

    f. Do textového pole vystavitele vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal. 

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Další informace najdete v dokumentaci k přiblížení.[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Vytvořit test uživatele lupy

Aby se uživatelé Azure AD mohli přihlašovat k přiblížení, musí se zřídit k přiblížení. V případě přiblížení je zřizování ručním úkolem.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu **zvětšení** vaší společnosti jako správce.

2. Klikněte na kartu **Správa účtů** a potom klikněte na možnost **Správa uživatelů**.

3. V části Správa uživatelů klikněte na **Přidat uživatele**.

    ![Správa uživatelů](./media/zoom-tutorial/ic784703.png "Správa uživatelů")

4. Na stránce **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele](./media/zoom-tutorial/ic784704.png "Přidat uživatele")

    a. Jako **typ uživatele**vyberte **základní**.

    b. Do textového pole **e-maily** zadejte e-mailovou adresu platného účtu Azure AD, který chcete zřídit.

    c. Klikněte na **Přidat**.

> [!NOTE]
> K zřizování Azure Active Directory uživatelských účtů můžete použít jakékoli jiné nástroje pro vytvoření nebo rozhraní API pro přiblížení účtu uživatele, které poskytuje přiblížení.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici přiblížení na přístupovém panelu byste měli být automaticky přihlášeni k přiblížení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si přiblížení pomocí Azure AD](https://aad.portal.azure.com/)
