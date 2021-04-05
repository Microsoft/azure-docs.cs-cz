---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s použitím záznamového připojení (SSO) – matic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a záznamem dění na matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.openlocfilehash: 2b0c42046df716c8ae65046e5f3314817da0a17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893773"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu záznamového připojení (SSO) k matic

V tomto kurzu se dozvíte, jak integrovat záznam dění na více matic s Azure Active Directory (Azure AD). Při integraci záznamového připojení (dění)-O-Matic s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k záznamům dění na základě matic.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do záznamového pracovníka-O-Matic s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Záznam dění na více záznamech (matic) – odběr povolený jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Záznam dění na webu – matic podporuje jednotné přihlašování spouštěné v **SP**
* Záznam dění na záznamovém čase – matic podporuje zřizování uživatelů **jenom včas**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Přidání dění na záznam dění a výstup-matic z Galerie

Pokud chcete nakonfigurovat integraci záznamového připojení (matic) do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat záznam dění na pracovní postup-O-Matic z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **záznam dění a výstup-matic** .
1. Vyberte položku **záznam dění na matic** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Konfigurace a testování jednotného přihlašování Azure AD pro záznam dění na záznamovém zařízení-O-Matic

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí záznamového připojení (matic) s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v záznamovém prostředí (dění v pracovním stupni)-O-matic.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí záznamového připojení (matic), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace záznamového připojení (dění)-o-matic SSO](#configure-screencast-o-matic-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte záznam dění na záznamovém zařízení (matic](#create-screencast-o-matic-test-user)** ). Pokud chcete, aby byl protějšek B. Simon v dění na záznamovém zařízení, které je propojeno s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací pro **záznam dění** v/v matic najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Chcete-li získat hodnotu, obraťte se na [tým podpory záznamem dění na straně klienta (matic](mailto:support@screencast-o-matic.com) ). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit záznam dění v/v-matic** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k záznamům dění na záznam dění na záznamovém počítači-O-matic.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte položku záznam dění na úrovni myši **-O-Matic**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-screencast-o-matic-sso"></a>Konfigurace záznamového připojení (dění)-O-Matic SSO

1. Pokud chcete automatizovat konfiguraci v rámci záznamového prostředí (matic), je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit záznam dění na záznamovém připojení (matic** ), které vás přesměruje do aplikace záznamového připojení Odtud zadejte přihlašovací údaje správce, které se přihlaste k režimu dění na záznamového připojení-O-matic. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-11.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit záznam dění na matic ručně, otevřete nové okno webového prohlížeče a přihlaste se ke svému webu společnosti na záznamovém připojení (dění v matic) jako správce a proveďte následující kroky:

1. Klikněte na **předplatné**.

    ![Předplatné](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. V části **přístupová stránka** klikněte na **Nastavení**.

    ![Snímek obrazovky, který zobrazuje oddíl "Access Page" s vybraným tlačítkem "nastavení".](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na **stránce přístup pro instalaci** proveďte následující kroky.

1. V části **Adresa URL přístupu** zadejte do zadaného textového pole svoji instanci.

    ![Snímek obrazovky, který zobrazuje oddíl "Access U R L" s zvýrazněným textovým polem název instance](./media/screencast-tutorial/tutorial_screencast_access.png)

1. V části **omezení uživatele v SAML (volitelné)** vyberte **vyžadovat uživatele domény** .

1. V části **nahrát soubor XML s metadaty IDP** klikněte na **zvolit soubor** a nahrajte metadata, která jste stáhli z Azure Portal.

1. Klikněte na **OK**.

    ![Přístup](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Vytvořit záznam dění na záznamovém zařízení-O-Matic testovací uživatel

V této části se uživatel s názvem Britta Simon vytvoří v dění na záznamovém dění-O-matic. Záznam dění na matic podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v záznamovém prostředí (dění v/v-matic neexistuje), vytvoří se po ověření nový. Pokud potřebujete ručně vytvořit uživatele, obraťte se na pracovní postup na [záznam dění na straně klienta (matic](mailto:support@screencast-o-matic.com)).

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici záznam dění na Maticu na přístupovém panelu byste měli být automaticky přihlášeni k pracovním postupům pro záznam dění na matic, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si záznam dění v matic s Azure AD](https://aad.portal.azure.com/)