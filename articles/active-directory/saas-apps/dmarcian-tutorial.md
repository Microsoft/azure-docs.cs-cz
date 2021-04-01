---
title: 'Kurz: Azure Active Directory integrace s dmarcian | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454724"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Kurz: integrace dmarcian s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat dmarcian s Azure Active Directory (Azure AD). Když integrujete dmarcian s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k dmarcian.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k dmarcian svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* dmarcian odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* dmarcian podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-dmarcian-from-the-gallery"></a>Přidání dmarcian z Galerie

Pokud chcete nakonfigurovat integraci dmarcian do služby Azure AD, musíte přidat dmarcian z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **dmarcian** .
1. Na panelu výsledků vyberte **dmarcian** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí dmarcian pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v dmarcian.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí dmarcian, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte DMARCIAN SSO](#configure-dmarcian-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte dmarcian Test User](#create-dmarcian-test-user)** -to, abyste měli protějšek B. Simon v dmarcian, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Dmarcian** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty budete aktualizovat skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On, která je vysvětlena dále v tomto kurzu.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurace jednotného přihlašování dmarcian

1. Pokud chcete automatizovat konfiguraci v rámci dmarcian, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Dmarcian nastavení** a nasměrujte vás na aplikaci dmarcian. Odtud zadejte přihlašovací údaje správce, které se přihlásí k dmarcian. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit dmarcian ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu dmarcian společnosti jako správce a proveďte následující kroky:

4. Klikněte na **profil** v pravém horním rohu a přejděte na **Předvolby**.

    ![Předvolby](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Posuňte se dolů a klikněte na část **jednotné přihlašování** a pak klikněte na **Konfigurovat**.

    ![Jedna](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na stránce **jednotného přihlašování SAML** nastavte **stav** **povoleno** a proveďte následující kroky:

    ![Ověřování](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * V části **Přidat dmarcian do svého poskytovatele identity** klikněte na **Kopírovat** a zkopírujte **adresu URL služby pro příjemce kontrolního výrazu** pro vaši instanci a vložte ji do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    * V části **Přidat dmarcian do svého poskytovatele identity** kliknutím na **Kopírovat** zkopírujte **ID entity** pro vaši instanci a vložte ji do textového pole **identifikátoru** v **základní části Konfigurace SAML** na Azure Portal.

    * V části **nastavit ověřování** v poli **metadata poskytovatele identity** vložte **adresu URL federačních metadat aplikace**, kterou jste zkopírovali z Azure Portal.

    * V části **nastavit ověřování** v textovém poli pro **Příkazy atributu** vložte adresu URL. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * V části **nastavit adresu URL pro přihlášení** ZKOPÍRUJTE **přihlašovací adresu** pro vaši instanci a vložte ji do textového pole **přihlašovací adresa URL** v **základní části Konfigurace SAML** na Azure Portal.

        > [!Note]
        > **Přihlašovací adresu URL** můžete upravit podle vaší organizace.

    * Klikněte na **Uložit**.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k dmarcian.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **dmarcian**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-dmarcian-test-user"></a>Vytvořit testovacího uživatele dmarcian

Aby se uživatelé Azure AD mohli přihlašovat k dmarcian, musí se zřídit v dmarcian. V dmarcian je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k dmarcian jako správce zabezpečení.

2. Klikněte na **profil** v pravém horním rohu a přejděte ke **správě uživatelů**.

    ![Uživatel](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Na pravé straně oddílu **Uživatelé jednotného přihlašování** klikněte na **Přidat nového uživatele**.

    ![Přidat uživatele](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. V místní nabídce **Přidat nového uživatele** proveďte následující kroky:

    ![Nový uživatel](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Do textového pole **E-mail nového uživatele** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com**.

    b. Pokud chcete uživateli udělit práva pro správu, vyberte **nastavit uživatele jako správce**.

    c. Klikněte na **Add User** (Přidat uživatele).

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici dmarcian, měli byste se automaticky přihlásit k dmarcian, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)