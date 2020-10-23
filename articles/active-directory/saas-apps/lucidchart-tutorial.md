---
title: 'Kurz: Azure Active Directory integrace s Lucidchart | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 3216718f076140cc9e7a5dec9048ee04adc5d96b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Lucidchart

V tomto kurzu se dozvíte, jak integrovat Lucidchart s Azure Active Directory (Azure AD). Když integrujete Lucidchart s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Lucidchart.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Lucidchart svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Lucidchart odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Lucidchart podporuje jednotné přihlašování iniciované v **SP**
* Lucidchart podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování Lucidchart můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Přidání Lucidchart z Galerie

Pokud chcete nakonfigurovat integraci Lucidchart do služby Azure AD, musíte přidat Lucidchart z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Lucidchart** .
1. Na panelu výsledků vyberte **Lucidchart** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Lucidchart

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Lucidchart pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Lucidchart.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Lucidchart, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte LUCIDCHART SSO](#configure-lucidchart-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Lucidchart Test User](#create-lucidchart-test-user)** -to, abyste měli protějšek B. Simon v Lucidchart, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Lucidchart** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   Do textového pole **přihlašovací adresa URL** zadejte adresu URL jako:  `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Lucidchart** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Lucidchart.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Lucidchart**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-lucidchart-sso"></a>Konfigurace jednotného přihlašování Lucidchart

1. V jiném okně webového prohlížeče se přihlaste k webu Lucidchart společnosti jako správce.

2. V nabídce v horní části klikněte na možnost **tým**.

    ![Team](./media/lucidchart-tutorial/ic791190.png "Tým")

3. Klikněte na **aplikace \> Spravovat SAML**.

    ![Spravovat SAML](./media/lucidchart-tutorial/ic791191.png "Spravovat SAML")

4. Na stránce **nastavení ověřování SAML** proveďte následující kroky:

    a. Vyberte **Povolit ověřování SAML**a pak klikněte na **volitelné**.

    ![Nastavení ověřování SAML](./media/lucidchart-tutorial/ic791192.png "Nastavení ověřování SAML")

    b. Do textového pole **doména** zadejte doménu a pak klikněte na **změnit certifikát**.

    ![Změnit certifikát](./media/lucidchart-tutorial/ic791193.png "Změnit certifikát")

    c. Otevřete stažený soubor metadat, zkopírujte obsah a vložte ho do textového pole pro **odeslání metadat** .

    ![Nahrát metadata](./media/lucidchart-tutorial/ic791194.png "Nahrát metadata")

    d. Vyberte možnost **automaticky přidat nové uživatele do týmu**a pak klikněte na **Uložit změny**.

    ![Uložit změny](./media/lucidchart-tutorial/ic791195.png "Uložit změny")

### <a name="create-lucidchart-test-user"></a>Vytvořit testovacího uživatele Lucidchart

Pro konfiguraci zřizování uživatelů na Lucidchart neexistuje žádná položka akce.  Když se přiřazený uživatel pokusí přihlásit k Lucidchart pomocí přístupového panelu, Lucidchart zkontroluje, jestli uživatel existuje.  

Pokud ještě není k dispozici žádný uživatelský účet, automaticky se vytvoří pomocí Lucidchart.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Lucidchart, měli byste se automaticky přihlásit k Lucidchart, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Lucidchart s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Lucidchart pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)