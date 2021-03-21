---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s uživatelským portálem Zscaler B2B | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a uživatelským portálem B2B pro Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s uživatelským portálem Zscaler B2B

V tomto kurzu se dozvíte, jak integrovat uživatelský portál B2B Zscaler pomocí služby Azure Active Directory (Azure AD). Když integrujete portál Zscaler B2B User Portal s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k portálu User Portal Zscaler B2B.
* Umožněte uživatelům, aby se automaticky přihlásili k Zscaler User Portalu B2B pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povoleného jednotného přihlašování (SSO) uživatelského portálu Zscaler B2B.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Uživatelský portál Zscaler B2B podporuje **IDPy** iniciované jednotné přihlašování.

* Uživatelský portál Zscaler B2B podporuje zřizování uživatelů **jenom v čase**

* Jakmile nakonfigurujete uživatelský portál B2B pro Zscaler, můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Přidání uživatelského portálu Zscaler B2B z Galerie

Pokud chcete nakonfigurovat integraci uživatelského portálu B2B Zscaler do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat uživatelský portál Zscaler B2B z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Zscaler User Portal** .
1. Z panelu výsledků vyberte **Zscaler User Portal** z webu B2B a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Zscaler User Portal v B2B

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Zscaler User Portalu B2B pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Zscaler User Portalu B2B.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu Zscaler B2B User Portal, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování ZSCALER B2B User Portal](#configure-zscaler-b2b-user-portal-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele ZSCALER B2B User Portal](#create-zscaler-b2b-user-portal-test-user)** – Pokud chcete mít protějšek B. Simon v uživatelském portálu Zscaler B2B, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **uživatelského portálu Zscaler B2B** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klientů ZSCALER B2B pro uživatele](https://help.zscaler.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení uživatelského portálu B2B pro Zscaler** se zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k portálu User Portal Zscaler B2B.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Zscaler User Portal pro B2B**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Konfigurace jednotného přihlašování Zscaler B2B uživatelského portálu

1. Otevřete nové okno webového prohlížeče a přihlaste se k webovému portálu Zscaler B2B User Portal jako správce a proveďte následující kroky:

1. V levé části nabídky klikněte na **Správa** a přejděte do části **ověřování** klikněte na **Konfigurace IDP**.

    ![Správa správce privátního přístupu Zscaler](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. V pravém horním rohu klikněte na **Přidat konfiguraci IDP**. 

    ![Zscaler správce privátního přístupu IDP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Na stránce **Přidat konfiguraci IDP** proveďte následující kroky:
 
    ![Vybrat Zscaler správce privátního přístupu](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klikněte na **Vybrat soubor** a nahrajte stažený soubor metadat z Azure AD do pole pro **nahrání souboru metadat IDP** .

    b. Přečte **metadata IDP** z Azure AD a naplní všechny informace o polích, jak je uvedeno níže.

    ![Konfigurace Správce privátního přístupu Zscaler](./media/zscaler-b2b-user-tutorial/config.png)

    c. Vyberte pole doména z **domén** .
    
    d. Klikněte na **Uložit**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Vytvořit testovacího uživatele v Zscaler B2B User Portal

V této části se na uživatelském portálu Zscaler B2B vytvoří uživatel s názvem Britta Simon. Uživatelský portál Zscaler B2B podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel na portálu Zscaler B2B User Portal ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici User Portal Zscaler B2B na přístupovém panelu byste měli být automaticky přihlášení k portálu User Portal Zscaler B2B, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Zscaler User Portal pro B2B s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)