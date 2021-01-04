---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s StatusPage | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 40cb3c6e326cbacfbfb9879cfa92b5780d86fe99
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724200"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s StatusPage

V tomto kurzu se dozvíte, jak integrovat StatusPage s Azure Active Directory (Azure AD).
Integrace StatusPage s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k StatusPage.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k StatusPage (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s StatusPage potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným StatusPagem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* StatusPage podporuje jednotné přihlašování **IDP** .

## <a name="adding-statuspage-from-the-gallery"></a>Přidání StatusPage z Galerie

Pokud chcete nakonfigurovat integraci StatusPage do služby Azure AD, musíte přidat StatusPage z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **StatusPage** .
1. Na panelu výsledků vyberte **StatusPage** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Konfigurace a testování jednotného přihlašování Azure AD pro StatusPage

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí StatusPage na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v StatusPage.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí StatusPage postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte STATUSPAGE SSO](#configure-statuspage-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvoření StatusPage Test User](#create-statuspage-test-user)** – pro Britta Simon v StatusPage, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **AskYourTeam** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    | Identifikátor |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

     | Adresa URL odpovědi |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Obraťte se na tým podpory StatusPage na adrese [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) a vyžádejte si metadata, která jsou nutná ke konfiguraci jednotného přihlašování. 
    >
    > a. Z metadat Zkopírujte hodnotu vystavitele a vložte ji do textového pole **identifikátoru** .
    >
    > b. Z metadat zkopírujte adresu URL odpovědi a vložte ji do textového pole **Adresa URL odpovědi** .

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení StatusPage** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k StatusPage.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **StatusPage**.

2. V seznamu aplikace vyberte **StatusPage**.

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-statuspage-sso"></a>Konfigurace jednotného přihlašování StatusPage

1. Pokud chcete automatizovat konfiguraci v rámci StatusPage, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit StatusPage** , které vás přesměruje do aplikace StatusPage. Odtud zadejte přihlašovací údaje správce, které se přihlásí k StatusPage. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit StatusPage ručně, v jiném okně webového prohlížeče se přihlaste k webu StatusPage společnosti jako správce.

1. Na hlavním panelu nástrojů klikněte na **Spravovat účet**.

    ![Snímek obrazovky zobrazuje účet pro správu vybraný na webu StatusPage společnosti.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **jednotného přihlašování** .

    ![Snímek obrazovky se zobrazí na kartě jednotného přihlašování.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce S nastavením S S O, kde můžete zadat hodnoty, které jsou popsány.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Snímek obrazovky se zobrazí tlačítko Uložit konfiguraci.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Do textového pole **Adresa URL cíle jednotného přihlašování** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikátu** .

    c. Klikněte na **Uložit konfiguraci**.

### <a name="create-statuspage-test-user"></a>Vytvořit testovacího uživatele StatusPage

Cílem této části je vytvořit uživatele s názvem Britta Simon v StatusPage.

StatusPage podporuje zřizování za běhu. V [konfiguraci jednotného přihlašování Azure AD](#configure-azure-ad-sso)jste ho už povolili.

**Chcete-li vytvořit uživatele s názvem Britta Simon v StatusPage, proveďte následující kroky:**

1. Přihlaste se k webu StatusPage společnosti jako správce.

1. V nabídce v horní části klikněte na **Spravovat účet**.

    ![Snímek obrazovky zobrazuje účet pro správu vybraný na webu StatusPage společnosti.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **Členové týmu** .
  
    ![Snímek obrazovky se zobrazí na kartě členové týmu.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klikněte na **Přidat člena týmu**.
  
    ![Snímek obrazovky se zobrazí tlačítko Přidat člena týmu.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Zadejte **e-mailovou adresu**, **jméno** a **příjmení** platného uživatele, kterého chcete zřídit, do příslušných textových polí. 

    ![Snímek obrazovky se zobrazí dialogové okno Přidat uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **role** vyberte **Správce klienta**.

1. Klikněte na **vytvořit účet**.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k StatusPage, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici StatusPage v okně moje aplikace, měli byste se automaticky přihlásit k StatusPage, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování StatusPage můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
