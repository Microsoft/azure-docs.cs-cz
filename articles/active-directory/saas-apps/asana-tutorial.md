---
title: 'Kurz: Azure Active Directory integrace s Asana | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651004"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Azure Active Directory integrace s Asana

V tomto kurzu se dozvíte, jak integrovat Asana s Azure Active Directory (Azure AD). Když integrujete Asana s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Asana.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Asana svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Asana odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Asana podporuje jednotné přihlašování iniciované v **SP**

* Asana podporuje [ **automatizované** zřizování uživatelů](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Přidání Asana z Galerie

Pokud chcete nakonfigurovat integraci Asana do služby Azure AD, musíte přidat Asana z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Asana** .
1. Na panelu výsledků vyberte **Asana** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Asana

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Asana pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Asana.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Asana postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Asana SSO](#configure-asana-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Asana Test User](#create-asana-test-user)** -to, abyste měli protějšek B. Simon v Asana, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Asana** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Asana a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://app.asana.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://app.asana.com/`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Asana** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Asana.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Asana**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-asana-sso"></a>Konfigurace jednotného přihlašování Asana

1. V jiném okně prohlížeče se přihlaste k aplikaci Asana. Pokud chcete nakonfigurovat jednotné přihlašování v Asana, přejděte do nastavení pracovního prostoru kliknutím na název pracovního prostoru v pravém horním rohu obrazovky. Pak klikněte na **\<your workspace name\> Nastavení**.

    ![Nastavení jednotného přihlašování Asana](./media/asana-tutorial/settings.png)

2. V okně **Nastavení organizace** klikněte na **Správa**. Pak klikněte na **Členové se musí přihlásit přes SAML** a povolit konfiguraci jednotného přihlašování. Proveďte následující kroky:

    ![Konfigurovat nastavení jedné Sign-On organizace](./media/asana-tutorial/save.png)  

    a. Do textového pole **Adresa URL přihlašovací stránky** vložte **přihlašovací adresu URL**.

    b. Klikněte pravým tlačítkem na certifikát stažený z Azure Portal a pak otevřete soubor certifikátu pomocí poznámkového bloku nebo preferovaného textového editoru. Zkopírujte obsah mezi nadpisem začátek a konec certifikátu a vložte ho do textového pole **certifikát X. 509** .

3. Klikněte na **Uložit**. Pokud potřebujete další pomoc, přečtěte si [Průvodce Asana pro nastavení jednotného přihlašování](https://asana.com/guide/help/premium/authentication#gl-saml) .

### <a name="create-asana-test-user"></a>Vytvořit testovacího uživatele Asana

Cílem této části je vytvořit uživatele s názvem Britta Simon v Asana. Asana podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](asana-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte prosím následující kroky:**

V této části vytvoříte uživatele s názvem Britta Simon v Asana.

1. V **Asana** přejdete do části **týmy** na levém panelu. Klikněte na tlačítko se symbolem plus.

    ![Vytvoření testovacího uživatele Azure AD](./media/asana-tutorial/teams.png)

2. Do textového pole zadejte e-mail uživatele, jako je **Britta. simon \@ contoso.com** , a pak vyberte **pozvat**.

3. Klikněte na **Odeslat pozvánku**. Nový uživatel dostane e-mail na svůj e-mailový účet. uživatel bude muset účet vytvořit a ověřit.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Asana, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Asana přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Asana v části Moje aplikace, přesměruje se na přihlašovací adresu Asana. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Asana můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).