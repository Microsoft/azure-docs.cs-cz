---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s ThousandEyes | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: aa3c5115a5255d30decbc66691878ffbe2579a06
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514584"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ThousandEyes

V tomto kurzu se dozvíte, jak integrovat ThousandEyes s Azure Active Directory (Azure AD). Když integrujete ThousandEyes s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ThousandEyes.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ThousandEyes svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ThousandEyes odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ThousandEyes podporuje jednotné přihlašování (SSO) **a IDP** .
* ThousandEyes podporuje [ **automatizované** zřizování uživatelů](./thousandeyes-provisioning-tutorial.md)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-thousandeyes-from-the-gallery"></a>Přidání ThousandEyes z Galerie

Pokud chcete nakonfigurovat integraci ThousandEyes do služby Azure AD, musíte přidat ThousandEyes z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ThousandEyes** .
1. Na panelu výsledků vyberte **ThousandEyes** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ThousandEyes

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ThousandEyes pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ThousandEyes.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ThousandEyes, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte THOUSANDEYES SSO](#configure-thousandeyes-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte ThousandEyes Test User](#create-thousandeyes-test-user)** -to, abyste měli protějšek B. Simon v ThousandEyes, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ThousandEyes** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.thousandeyes.com/login/sso`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení ThousandEyes** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ThousandEyes.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ThousandEyes**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-thousandeyes-sso"></a>Konfigurace jednotného přihlašování ThousandEyes

1. V jiném okně webového prohlížeče se přihlaste k webu **ThousandEyes** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení**.

    ![Snímek obrazovky se zobrazí web ThousandEyes s vybraným nastavením.](./media/thousandeyes-tutorial/ic790066.png "Nastavení")

3. Kliknout na **účet**

    ![Snímek obrazovky zobrazuje účet vybraný v nabídce nastavení.](./media/thousandeyes-tutorial/ic790067.png "Účet")

4. Klikněte na kartu **ověřování & zabezpečení** .

    ![Ověřování & zabezpečení](./media/thousandeyes-tutorial/ic790068.png "Ověřování & zabezpečení")

5. V části **nastavení jednotného přihlašování** proveďte následující kroky:

    ![Nastavit jednotné přihlašování](./media/thousandeyes-tutorial/ic790069.png "Nastavit jeden Sign-On")

    a. Vyberte **Povolit jednotné přihlašování**.

    b. Do textového pole **Adresa URL přihlašovací stránky** vložte **přihlašovací adresu URL**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL odhlašovací stránky** vložte **adresu URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **vystavitele zprostředkovatele identity** vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal.

    e. V **ověřovacím certifikátu**klikněte na **zvolit soubor**a pak Nahrajte certifikát, který jste stáhli z Azure Portal.

    f. Klikněte na **Uložit**.

### <a name="create-thousandeyes-test-user"></a>Vytvořit testovacího uživatele ThousandEyes

Cílem této části je vytvořit uživatele s názvem Britta Simon v ThousandEyes. ThousandEyes podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](thousandeyes-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k webu ThousandEyes společnosti jako správce.

2. Klikněte na **Nastavení**.

    ![Snímek obrazovky se zobrazí web ThousandEyes s vybraným nastavením.](./media/thousandeyes-tutorial/ic790066.png "Nastavení")

3. Klikněte na **účet**.

    ![Snímek obrazovky zobrazuje účet vybraný v nabídce nastavení.](./media/thousandeyes-tutorial/ic790067.png "Účet")

4. Klikněte na kartu **účty & uživatelé** .

    ![Účty & uživatelů](./media/thousandeyes-tutorial/IC790073.png "Účty & uživatelů")

5. V části **Přidat uživatele & účty** proveďte následující kroky:

    ![Přidání uživatelských účtů](./media/thousandeyes-tutorial/IC790074.png "Přidání uživatelských účtů")

    a. Do textového pole **název** zadejte jméno uživatele jako **B. Simon**.

    b. Do textového pole text **e-mailu** zadejte e-maily jako uživatel b.simon@contoso.com .

    b. Klikněte na **Přidat nového uživatele k účtu**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail, včetně odkazu na potvrzení a aktivaci účtu.

> [!NOTE]
> K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů ThousandEyes nebo rozhraní API poskytovaná ThousandEyes.


## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ThousandEyes, měli byste se automaticky přihlásit k ThousandEyes, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si ThousandEyes s Azure AD](https://aad.portal.azure.com/)

- [Konfigurace zřizování uživatelů](./thousandeyes-provisioning-tutorial.md)