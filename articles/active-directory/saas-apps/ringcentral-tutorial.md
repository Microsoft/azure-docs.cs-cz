---
title: 'Kurz: Azure Active Directory integrace s RingCentral | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 20e59f134c51662f9530862790d5a47b8bb21d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650718"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Kurz: integrace RingCentral s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat RingCentral s Azure Active Directory (Azure AD). Když integrujete RingCentral s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k RingCentral.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k RingCentral svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* RingCentral odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RingCentral podporuje jednotné přihlašování (SSO) iniciované **IDP** .

## <a name="add-ringcentral-from-the-gallery"></a>Přidání RingCentral z Galerie

Pokud chcete nakonfigurovat integraci RingCentral do služby Azure AD, musíte přidat RingCentral z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **RingCentral** .
1. Na panelu výsledků vyberte **RingCentral** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Konfigurace a testování jednotného přihlašování Azure AD pro RingCentral

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí RingCentral pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v RingCentral.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí RingCentral postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte RINGCENTRAL SSO](#configure-ringcentral-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte RingCentral Test User](#create-ringcentral-test-user)** -to, abyste měli protějšek B. Simon v RingCentral, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **RingCentral** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    1. Klikněte na **nahrát soubor metadat**.
    1. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.
    1. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** pro **identifikátor** a odpověď získají automaticky v **základní části Konfigurace SAML** .

    > [!Note]
    > **Soubor metadat poskytovatele služeb** získáte na stránce konfigurace jednotného přihlašování RingCentral, která je vysvětlena dále v tomto kurzu.

1. Pokud nemáte **soubor metadat poskytovatele služby**, zadejte hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte jednu z adres URL:
  
    | Identifikátor |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Do textového pole **Adresa URL odpovědi** zadejte jednu z adres URL:

    | Adresa URL odpovědi |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k RingCentral.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **RingCentral**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ringcentral-sso"></a>Konfigurace jednotného přihlašování RingCentral

1. Pokud chcete automatizovat konfiguraci v rámci RingCentral, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit RingCentral** , které vás přesměruje do aplikace RingCentral. Odtud zadejte přihlašovací údaje správce, které se přihlásí k RingCentral. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit RingCentral ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu RingCentral společnosti jako správce a proveďte následující kroky:

1. V horní části klikněte na **nástroje**.

    ![Snímek obrazovky ukazuje nástroje vybrané z webu společnosti RingCentral.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Přejděte do **jednotného přihlašování**.

    ![Snímek obrazovky zobrazuje jednu Sign-On vybranou v nabídce nástroje.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Na stránce **jednotného přihlašování** v části **Konfigurace jednotného přihlašování** klikněte v **kroku 1** na **Upravit** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na stránce konfigurace S S/O, kde můžete vybrat upravit.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Na stránce **nastavit jednotné přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se stránkou nastavit jednu Sign-On, kde můžete nahrávat I D P metadat.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Klikněte na tlačítko **Procházet** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    b. Po nahrání metadat se hodnoty získají automaticky vyplněné v části **Obecné informace jednotného přihlašování** .

    c. V části **mapování atributů** vyberte **mapovat atribut e-mailu na** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klikněte na **Uložit**.

    e. V **kroku 2** klikněte na **Stáhnout** a stáhněte **si soubor metadat poskytovatele služby** a nahrajte ho do **základního konfiguračního oddílu SAML** , aby se automaticky vyplnily hodnoty **identifikátoru** **URL pro odpovědi** v Azure Portal.

    ![Snímek obrazovky se zobrazí na stránce konfigurace S S/O, kde můžete vybrat stáhnout.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Na stejné stránce přejděte k části **povolení jednotného přihlašování** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části povolení S/O, kde můžete konfiguraci dokončit.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Vyberte možnost **Povolit službu jednotného přihlašování**.

    * Vyberte možnost **dovolit uživatelům přihlášení pomocí jednotného přihlašování nebo přihlašovacích údajů RingCentral**.

    * Klikněte na **Uložit**.

### <a name="create-ringcentral-test-user"></a>Vytvořit testovacího uživatele RingCentral

V této části vytvoříte uživatele s názvem Britta Simon v RingCentral. Pokud chcete přidat uživatele na platformě RingCentral, pracujte s [týmem podpory klienta RingCentral](https://success.ringcentral.com/RCContactSupp) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k RingCentral, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici RingCentral v okně moje aplikace, měli byste se automaticky přihlásit k RingCentral, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování RingCentral můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).