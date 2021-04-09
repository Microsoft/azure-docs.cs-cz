---
title: 'Kurz: Azure Active Directory integrace s AlertOps | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AlertOps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 094b1a029c8e10abce3ca282de0cc99be34d313a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92318860"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Kurz: integrace AlertOps s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat AlertOps s Azure Active Directory (Azure AD). Když integrujete AlertOps s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AlertOps.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AlertOps svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AlertOps odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. AlertOps podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-alertops-from-the-gallery"></a>Přidání AlertOps z Galerie

Pokud chcete nakonfigurovat integraci AlertOps do služby Azure AD, musíte přidat AlertOps z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AlertOps** .
1. Na panelu výsledků vyberte **AlertOps** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AlertOps pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v AlertOps.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AlertOps, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte AlertOps](#configure-alertops)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele AlertOps test](#create-alertops-test-user)** , který má protějšek Britta Simon v AlertOps, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **AlertOps** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    1. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.alertops.com`

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta AlertOps](mailto:support@alertops.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení AlertOps** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurace AlertOps

1. Pokud chcete automatizovat konfiguraci v rámci AlertOps, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **AlertOps nastavení** a nasměrujte vás na aplikaci AlertOps. Odtud zadejte přihlašovací údaje správce, které se přihlásí k AlertOps. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit AlertOps ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu AlertOps společnosti jako správce a proveďte následující kroky:

4. V levém navigačním panelu klikněte na **Nastavení účtu** .

    ![Snímek obrazovky se zobrazí v nabídce AlertOps s nastavením účtu s názvem.](./media/alertops-tutorial/configure1.png)

5. Na stránce **nastavení odběru** vyberte **jednotné přihlašování** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí okno nastavení předplatného pro s s s hodnotami, které jsou zadány, jak je popsáno v tomto kroku.](./media/alertops-tutorial/configure2.png)

    a. Zaškrtněte políčko **použít jednotné přihlašování (SSO)** .

    b. Z rozevíracího seznamu vyberte **Azure Active Directory** jako **poskytovatele jednotného přihlašování** .

    c. V textovém poli **Adresa URL vystavitele** použijte hodnotu identifikátoru, kterou jste použili v **základní části konfigurace SAML** v Azure Portal.

    d. Do textového pole **Adresa URL koncového bodu SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL koncového bodu slo** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    f. V rozevíracím seznamu vyberte **SHA256** jako **algoritmus podpisu SAML** .

    například Otevřete stažený soubor certifikátu (Base64) v programu Poznámkový blok. Zkopírujte obsah této složky do schránky a vložte ji do textového pole certifikát X. 509.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k AlertOps.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AlertOps**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-alertops-test-user"></a>Vytvořit testovacího uživatele AlertOps

1. V jiném okně prohlížeče se přihlaste k webu AlertOps společnosti jako správce.

2. V levém navigačním panelu klikněte na **uživatele** .

    ![Snímek obrazovky se zobrazí v nabídce AlertOps s uživateli s názvem.](./media/alertops-tutorial/user1.png)

3. Vyberte **Přidat uživatele**.

    ![Snímek obrazovky s tlačítkem Přidat uživatele zobrazí okno uživatelé.](./media/alertops-tutorial/user2.png)

4. V dialogovém okně **Přidat uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v podokně Přidat uživatele s hodnotami uvedenými v tomto kroku.](./media/alertops-tutorial/user3.png)

    a. Do textového pole **přihlašovací uživatelské jméno** zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    b. Do textového pole **oficiální e-mail** zadejte e-mailovou adresu uživatele, jako je **Brittasimon \@ contoso.com**.

    c. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například **Britta**.

    d. Do textového pole **příjmení** zadejte jméno uživatele, jako je **Simon**.

    e. V rozevíracím seznamu vyberte hodnotu **typu** podle vaší organizace.

    f. V rozevíracím seznamu vyberte **roli** uživatele podle vaší organizace.

    například Vyberte **Přidat**.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici AlertOps, měli byste se automaticky přihlásit k AlertOps, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)