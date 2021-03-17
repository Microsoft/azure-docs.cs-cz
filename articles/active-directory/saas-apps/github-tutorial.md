---
title: 'Kurz: Azure Active Directory integrace s organizací GitHub Enterprise cloudu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a firemní cloudovou organizací GitHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732143"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k organizaci GitHub Enterprise Cloud

V tomto kurzu se dozvíte, jak integrovat **organizaci** z webu GitHub Enterprise Cloud s Azure Active Directory (Azure AD). Když integrujete organizaci GitHub Enterprise cloudu s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k vaší podnikové cloudové organizaci GitHubu.
* Spravujte přístup ke svojí podnikové cloudové organizaci GitHubu v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s podnikovou cloudovou organizací GitHub potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Organizace GitHubu vytvořená v [GitHub Enterprise cloudu](https://help.github.com/articles/github-s-products/#github-enterprise), která vyžaduje [fakturační plán pro GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* GitHub podporuje jednotné přihlašování (SSO) s podporou **SP**

* GitHub podporuje [ **automatizované** zřizování uživatelů (pozvánky organizace).](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>Přidání GitHubu z Galerie

Pokud chcete nakonfigurovat integraci GitHubu do Azure AD, musíte přidat GitHub z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **GitHub** .
1. Vyberte **GitHub Enterprise Cloud – organizace** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro GitHub

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí GitHubu pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v GitHubu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí GitHubu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování GitHubu](#configure-github-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si testovacího uživatele GitHubu](#create-github-test-user)** – abyste měli protějšek B. Simon v GitHubu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **GitHubu** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://github.com/orgs/<Organization ID>/sso`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://github.com/orgs/<Organization ID>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Počítejte s tím, že se nejedná o reálné hodnoty. Tyto hodnoty musíte aktualizovat pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Tady doporučujeme, abyste v identifikátoru použili jedinečnou hodnotu řetězce. K načtení těchto hodnot použijte část správce GitHubu.

5. Vaše aplikace GitHub očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde je jako **jedinečný identifikátor uživatele (ID názvu)** namapována hodnota **User. userPrincipalName**. Aplikace GitHub očekává, že **jedinečný identifikátor uživatele (ID)** má být namapován pomocí **User. mail**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![Snímek obrazovky, který zobrazuje oddíl "atributy uživatele" se zvolenou ikonou "Upravit".](common/edit-attribute.png)

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení GitHubu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k GitHubu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **GitHub**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.

    ![role uživatele](./media/github-tutorial/user-role.png)

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-github-sso"></a>Konfigurace jednotného přihlašování GitHubu

1. V jiném okně webového prohlížeče se přihlaste k webu GitHub do vaší organizace jako správce.

2. Přejděte do **Nastavení** a klikněte na **zabezpečení**.

    ![Snímek obrazovky, který zobrazuje nabídku nastavení organizace s vybraným zabezpečením](./media/github-tutorial/security.png)

3. Zaškrtněte pole **Povolit ověřování SAML** , které odhalí konfigurační pole jednotného přihlašování, proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje část S jednotným přihlašováním "S A M l" se zvýrazněnými textovými poli "Povolit ověřování S M L" A U R L.](./media/github-tutorial/saml-sso.png)

    a. Zkopírujte hodnotu **adresy URL jednotného přihlašování** a vložte tuto hodnotu do textového pole **přihlašovací adresa URL** v **základní konfiguraci SAML** v Azure Portal.
    
    b. Zkopírujte hodnotu **adresy url assertion Consumer Service** a vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v **základní konfiguraci SAML** v Azure Portal.

4. Nakonfigurujte následující pole:

    ![Snímek obrazovky s textovými poli pro přihlášení k adrese URL, vystavitele a veřejný certifikát](./media/github-tutorial/configure.png)

    a. Do textového pole **přihlašování URL** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal v programu Poznámkový blok, vložte obsah do textového pole **veřejné certifikáty** .

    d. Kliknutím na ikonu **Upravit** upravíte **metodu podpisu** a **metodu Digest** z **RSA-SHA1** a **SHA1** na **RSA-SHA256** a **SHA256** , jak je znázorněno níže.
    
    e. Aktualizujte adresu URL **assertion Consumer Service (adresa URL odpovědi)** z výchozí adresy URL tak, aby se adresa URL v GitHubu shodovala s adresou URL v registraci aplikace Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kliknutím na **Test konfigurace SAML** potvrďte, že během jednotného přihlašování nedochází k selhání ověřování nebo chybám.

    ![Nastavení](./media/github-tutorial/test.png)

6. Klikněte na **Uložit**.

> [!NOTE]
> Jednotné přihlašování se v GitHubu ověřuje na konkrétní organizaci v GitHubu a nenahrazuje ověřování samotného GitHubu. Proto, pokud vypršela platnost relace github.com uživatele, můžete být během procesu jednotného přihlašování požádáni o ověření pomocí ID nebo hesla GitHubu.

### <a name="create-github-test-user"></a>Vytvořit testovacího uživatele GitHubu

Cílem této části je vytvořit uživatele s názvem Britta Simon na GitHubu. GitHub podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](github-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k webu GitHub společnosti jako správce.

2. Klikněte na **lidé**.

    ![Snímek obrazovky zobrazuje web GitHub s vybranými lidmi.](./media/github-tutorial/people.png "People")

3. Klikněte na **pozvat člena**.

    ![Pozvat uživatele](./media/github-tutorial/invite-member.png "Pozvat uživatele")

4. Na stránce **pozvání členů** proveďte následující kroky:

    a. Do textového pole **e-mail** zadejte e-mailovou adresu účtu Britta Simon.

    ![Pozvat lidi](./media/github-tutorial/email-box.png "Pozvat lidi")

    b. Klikněte na **Odeslat pozvánku**.

    ![Snímek obrazovky s vybraným dialogovým oknem pro pozvání "Member" a vybraným tlačítkem pro odeslání pozvánky.](./media/github-tutorial/send-invitation.png "Pozvat lidi")

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení GitHubu, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení GitHubu přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici GitHubu v části Moje aplikace, přesměruje se na přihlašovací adresu URL GitHubu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování GitHubu můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)