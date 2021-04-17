---
title: 'Kurz: Azure Active Directory integrace s Clarizenem 1 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clarizen jednu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516825"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Kurz: Azure Active Directory integrace s Clarizen 1

V tomto kurzu se dozvíte, jak integrovat Clarizen s Azure Active Directory (Azure AD). Když integrujete Clarizen s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Clarizen.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Clarizen účtu s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Clarizen jedno předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Clarizen jedna podporuje **IDP** , které iniciovaly jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-clarizen-one-from-the-gallery"></a>Přidání Clarizen z Galerie

Pokud chcete nakonfigurovat integraci Clarizen do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Clarizen jednu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Clarizen** .
1. Vyberte **Clarizen** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Clarizen One

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Clarizen s jedním pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Clarizen.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s Clarizen jedním, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte CLARIZEN jednotné přihlašování](#configure-clarizen-one-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Clarizen](#create-clarizen-one-test-user)** , abyste měli protějšek B. Simon v Clarizen, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce věnované integraci aplikace **Clarizen** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte hodnotu: `Clarizen`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Clarizen 1** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Clarizen.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Clarizen jednu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name=&quot;configure-clarizen-one-sso&quot;></a>Konfigurace Clarizen jednoho jednotného přihlašování

1. V jiném okně webového prohlížeče se přihlaste k Clarizen jedné firemní síti jako správce.

1. Klikněte na své uživatelské jméno a pak klikněte na **Nastavení**.

    ![V části uživatelské jméno klikněte na nastavení.](./media/clarizen-tutorial/setting.png &quot;Nastavení")

1. Klikněte na kartu **globální nastavení** . Pak klikněte na **Upravit** vedle **federovaného ověřování**.

    ![Karta globální nastavení](./media/clarizen-tutorial/authentication.png "Globální nastavení")

1. V dialogovém okně **federované ověřování** proveďte následující kroky:

    ![Dialogové okno federované ověřování](./media/clarizen-tutorial/federated-authentication.png "Federované ověřování")

    a. Vyberte možnost **Povolit federované ověřování**.

    b. Kliknutím na **nahrát** nahrajte stažený certifikát.

    c. Do pole **Adresa URL pro přihlášení** zadejte hodnotu **přihlašovací adresa URL** z okna konfigurace aplikace služby Azure AD.

    d. V poli **Adresa URL pro** odhlášení zadejte hodnotu **URL pro odhlášení** z okna konfigurace aplikace služby Azure AD.

    e. Vyberte **použít příspěvek**.

    f. Klikněte na **Uložit**.

### <a name="create-clarizen-one-test-user"></a>Vytvořit Clarizen jednoho testovacího uživatele

Cílem této části je vytvořit uživatele s názvem Britta Simon v Clarizen One.

**Pokud potřebujete ručně vytvořit uživatele, proveďte prosím následující kroky:**

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Clarizen, musíte zřídit uživatelské účty. V případě Clarizen je zřizování ručním úkolem.

1. Přihlaste se k Clarizen jedné firemní lokality jako správce.

2. Klikněte na **lidé**.

    ![Kliknutí na lidé](./media/clarizen-tutorial/people.png "People")

3. Klikněte na **pozvat uživatele**.

    ![Tlačítko pozvat uživatele](./media/clarizen-tutorial/user.png "Pozvat uživatele")

1. V dialogovém okně **pozvat lidi** proveďte následující kroky:

    ![Dialogové okno pozvat lidi](./media/clarizen-tutorial/invite-people.png "Pozvat lidi")

    a. Do pole **e-mail** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klikněte na **pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k Clarizen, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na Clarizen jednu dlaždici v okně moje aplikace byste měli být automaticky přihlášeni k Clarizen, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Clarizen můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).