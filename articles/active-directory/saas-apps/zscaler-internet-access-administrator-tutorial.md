---
title: 'Kurz: Azure Active Directory integrace s Zscaler Internet Access Administrator | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449310"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Kurz: Azure Active Directory integrace s Zscaler Internet Access Administrator

V tomto kurzu se dozvíte, jak integrovat Zscaler Internet Access Administrator s Azure Active Directory (Azure AD). Když integrujete Zscaler Internet Access Administrator s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Zscaler správce internetového přístupu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Zscalerí správce internetového přístupu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Zscaler předplatného jednotného přihlašování (SSO) správce internetového přístupu.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce internetového přístupu Zscaler podporuje jednotné přihlašování **IDP** .

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Přidání správce internetového přístupu Zscaler z Galerie

Pokud chcete nakonfigurovat integraci správce internetového přístupu Zscaler do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler správce internetového přístupu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Zscaler Internet Access Administrator** .
1. Na panelu výsledků vyberte **Zscaler Internet Access Administrator** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Zscaler Internet Access Administrator

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Správce Zscaler internetového přístupu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler Internet Access Administrator.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce Zscaler Internet Access, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování Zscaler pro Internet Access](#configure-zscaler-internet-access-administrator-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořit testovacího uživatele správce přístupu Zscaler k Internetu](#create-zscaler-internet-access-administrator-test-user)** – má protějšek Britta Simon v Zscaler správce internetového přístupu, který se odkazuje na reprezentaci uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **správce internetového přístupu Zscaler** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte jednu z následujících adres URL podle vašeho požadavku:

    | Identifikátor |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Do textového pole **Adresa URL odpovědi** zadejte jednu z následujících adres URL podle vašeho požadavku:

    | Adresa URL odpovědi |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Aplikace Zscaler pro správce internetového přístupu očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele & deklarace** na stránce integrace aplikací. Na **stránce nastavit jeden Sign-On se stránkou SAML** kliknutím na tlačítko **Upravit** otevřete **atributy uživatele &** dialogovém okně deklarace.

    ![Odkaz na atribut](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name  | Zdrojový atribut  |
    | ---------| ------------ |
    | Role | User. assignedroles |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. V seznamu **zdrojový atribut** vyberte hodnotu atributu.

    c. Klikněte na **OK**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Pokud chcete zjistit, jak nakonfigurovat roli v Azure AD, klikněte prosím [sem](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) .

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavení správce internetového přístupu Zscaler** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Zscaler správce přístupu k Internetu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler správce přístupu k Internetu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud jste nastavili role, jak je vysvětleno výše, můžete je vybrat v rozevíracím seznamu **Vybrat roli** .
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Konfigurace jednotného přihlašování pro správce internetového přístupu Zscaler

1. V jiném okně webového prohlížeče se přihlaste k uživatelskému rozhraní správce internetového přístupu Zscaler.

2. Přejděte do části **správa > Správa správců** a proveďte následující kroky a klikněte na Uložit:

    ![Snímek obrazovky zobrazuje správu správců s možnostmi pro povolení ověřování SAML, nahrajte certifikát S S L a zadat vystavitele.](./media/zscaler-internet-access-administrator-tutorial/management.png "Správa")

    a. Zaškrtněte **možnost povolit ověřování SAML**.

    b. Klikněte na **nahrát** a nahrajte podpisový certifikát Azure SAML, který jste stáhli z Azure Portal ve **veřejném certifikátu SSL**.

    c. Volitelně můžete pro další zabezpečení přidat údaje **vystavitele** k ověření vystavitele odpovědi SAML.

3. V uživatelském rozhraní správce proveďte následující kroky:

    ![Snímek obrazovky se zobrazí U správce U, kde můžete provést kroky.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. Najeďte myší na nabídku **Aktivace** v blízkosti levého dolního rohu.

    b. Klikněte na tlačítko **aktivovat**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Vytvořit testovacího uživatele pro správce Zscaler internetového přístupu

Cílem této části je vytvořit uživatele s názvem Britta Simon v Zscaler správce internetového přístupu. Zscaler Internet Access nepodporuje zřizování za běhu pro jednotné přihlašování (SSO) správce. Je nutné ručně vytvořit účet správce.
Postup vytvoření účtu správce najdete v dokumentaci k Zscaler:

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k správci internetového přístupu Zscaler, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Zscaler Internet Access Administrator v okně moje aplikace byste měli být automaticky přihlášeni k Zscaler správce internetového přístupu, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování správce internetového přístupu Zscaler můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
