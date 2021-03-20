---
title: 'Kurz: Azure Active Directory integrace s Fluxx Labs | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92452579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s využitím Fluxx Labs

V tomto kurzu se dozvíte, jak integrovat Fluxx Labs s Azure Active Directory (Azure AD). Když integrujete Fluxx Labs s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Fluxx Labs.
* Umožněte uživatelům, aby se do Fluxx Labs automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Fluxx Labs s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Fluxx Labs podporuje **IDPy** iniciované jednotné přihlašování.
* Po nakonfigurování Fluxx Labs můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z Galerie

Pokud chcete nakonfigurovat integraci Fluxx Labs do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Fluxx Labs z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Fluxx Labs** .
1. Na panelu výsledků vyberte **Fluxx Labs** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Fluxx Labs

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Fluxx Labs pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Fluxx Labs.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Fluxx Labs, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Fluxx Labs](#configure-fluxx-labs-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele Fluxx Labs](#create-fluxx-labs-test-user)** – můžete mít protějšek B. Simon v Fluxx Labs, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Fluxx Labs** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předběžná produkce | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předběžná produkce | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienty Fluxx Labs](https://fluxx.zendesk.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Fluxx Labs** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Fluxx Labs.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Fluxx Labs**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-fluxx-labs-sso"></a>Konfigurace jednotného přihlašování Fluxx Labs

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Fluxx Labs jako správce.

2. V části **Nastavení** vyberte **správce** .

    ![Snímek obrazovky, který zobrazuje oddíl Settings (nastavení) se zvoleným správcem](./media/fluxxlabs-tutorial/config1.png)

3. Na panelu pro správu vyberte integrace **s modulem plug-in**  >   a pak vyberte **jednotné přihlašování SAML – (zakázáno)** .

    ![Snímek obrazovky zobrazující kartu Integration (integrace) s vybraným S A M L S S O – (zakázaný)](./media/fluxxlabs-tutorial/config2.png)

4. V části atributu proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje oddíl Attributes s zaškrtnutým políčkem S A M L S S, hodnotami zadanými v polích a vybraným tlačítkem Uložit.](./media/fluxxlabs-tutorial/config3.png)

    a. Zaškrtněte políčko **SAML SSO** .

    b. Do textového pole **Cesta požadavku** zadejte **/auth/SAML**.

    c. Do textového pole **cesta zpětného volání** zadejte **/auth/SAML/callback**.

    d. Do textového pole **Adresa URL služby pro příjemce kontrolního výrazu (Single Sign-On URL)** zadejte hodnotu **adresy URL odpovědi** , kterou jste zadali v Azure Portal.

    e. Do textového pole **cílová skupina (ID entity SP)** zadejte hodnotu **identifikátoru** , kterou jste zadali v Azure Portal.

    f. Do textového pole **Adresa URL cíle jednotného přihlašování zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    například Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .

    h. Do textového pole **Formát identifikátoru názvu** zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Klikněte na **Uložit**.

    > [!NOTE]
    > Po uložení obsahu se pole pro zabezpečení zobrazí jako prázdné, ale hodnota se uloží do konfigurace.

### <a name="create-fluxx-labs-test-user"></a>Vytvořit testovacího uživatele pro Fluxx Labs

Aby se uživatelé Azure AD mohli přihlašovat k Fluxx Labs, musí se zřídit v Fluxx Labs. V případě Fluxx Labs je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k firemnímu webu Fluxx Labs jako správce.

2. Klikněte na **ikonu** zobrazené níže.

    ![Snímek obrazovky, který zobrazuje možnosti Správce se ikonou "plus" vybraným v "řídicí panel je prázdný".](./media/fluxxlabs-tutorial/config6.png)

3. Na řídicím panelu klikněte na ikonu níže zobrazené a otevřete kartu **nové osoby** .

    ![Snímek obrazovky, který zobrazuje nabídku "Správa kontaktů" se ikonou "plus" vedle možnosti "lidé".](./media/fluxxlabs-tutorial/config4.png)

4. V části **noví lidé** proveďte následující kroky:

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs jako jedinečný identifikátor pro přihlašovací jména jednotného přihlašování používá e-mail. Vyplňte pole **UID jednotného přihlašování** pomocí e-mailové adresy uživatele, která se shoduje s e-mailovou adresou, kterou používají jako přihlašovací údaje s SSO.

    b. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Fluxx Labs na přístupovém panelu, měli byste se automaticky přihlásit k Fluxx Labs, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte Fluxx Labs s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Fluxx Labs pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)