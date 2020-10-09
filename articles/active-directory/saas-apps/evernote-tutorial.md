---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Evernote | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: b22bf4ee3ff830a80ee97851f08173257697c50d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826485"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Evernote

V tomto kurzu se dozvíte, jak integrovat Evernote s Azure Active Directory (Azure AD). Když integrujete Evernote s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Evernote.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Evernote svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Evernote odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Evernote podporuje jednotné přihlašování (SSO) **a IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-evernote-from-the-gallery"></a>Přidání Evernote z Galerie

Pokud chcete nakonfigurovat integraci Evernote do služby Azure AD, musíte přidat Evernote z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Evernote** .
1. Na panelu výsledků vyberte **Evernote** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Evernote

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Evernote pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Evernote.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Evernote, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Evernote SSO](#configure-evernote-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Evernote Test User](#create-evernote-test-user)** -to, abyste měli protějšek B. Simon v Evernote, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Evernote** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **identifikátor** zadejte adresu URL:  `https://www.evernote.com/saml2`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://www.evernote.com/Login.action`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. Chcete-li upravit možnosti **podepisování** , klikněte na tlačítko **Upravit** a otevřete dialogové okno **podpisový certifikát SAML** .

    ![Snímek obrazovky s dialogovým oknem pro podpisový certifikát, který obsahuje vybrané tlačítko pro úpravy](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Pro **možnost podepisování**vyberte možnost **podepsat odpověď SAML a kontrolní výraz** .

    b. Klikněte na **Uložit**.

1. V části **Nastavení Evernote** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Evernote.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Evernote**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-evernote-sso"></a>Konfigurace jednotného přihlašování Evernote

1. Pokud chcete automatizovat konfiguraci v rámci Evernote, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Evernote nastavení** a nasměrujte vás na aplikaci Evernote. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Evernote. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Evernote ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Evernote společnosti jako správce a proveďte následující kroky:

4. Přejít do **konzoly pro správu**

    ![Správce – konzola](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Z **konzoly pro správu**, přejít na **zabezpečení** a vybrat **jednotné přihlašování**

    ![Jednotné přihlašování – nastavení](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Nakonfigurujte následující hodnoty:

    ![Nastavení certifikátu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Povolit jednotné přihlašování:** Jednotné přihlašování je ve výchozím nastavení povolené (pro odebrání požadavku SSO klikněte na **Zakázat jednotné přihlašování** ).

    b. Vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal do TEXTOVÉHO pole **URL požadavku SAML http** .

    c. Otevřete stažený certifikát z Azure AD v poznámkovém bloku a zkopírujte obsah včetně příkazu "BEGIN CERTIFICATE" a "END CERTIFICATE" a vložte ho do textového pole pro **certifikát X. 509** . 

    d. klikněte na **Uložit změny** .

### <a name="create-evernote-test-user"></a>Vytvořit testovacího uživatele Evernote

Aby se uživatelé Azure AD mohli přihlašovat k Evernote, musí se zřídit v Evernote.  
V případě Evernote je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se k webu Evernote společnosti jako správce.

2. Klikněte na **konzolu správce**.

    ![Správce – konzola](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z **konzoly pro správu**, přejít na **Přidat uživatele**.

    ![Snímek obrazovky, který zobrazuje nabídku uživatelé s vybranou možnost Přidat uživatele.](./media/evernote-tutorial/create_aaduser_0001.png)

4. Do textového pole **E-mail** **přidejte členy týmu** , zadejte e-mailovou adresu uživatelského účtu a klikněte na **pozvat.**

    ![Přidat-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po odeslání pozvánky obdrží Držitel účtu Azure Active Directory e-mail s požadavkem na přijetí pozvánky.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Evernote, měli byste se automaticky přihlásit k Evernote, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Evernote s Azure AD](https://aad.portal.azure.com/)

