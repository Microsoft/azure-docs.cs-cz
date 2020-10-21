---
title: 'Kurz: Azure Active Directory integrace s adaptivními přehledy | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a adaptivními přehledy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.openlocfilehash: 70c59e4352d41070c22b0e268ef1cf5cfa1c7211
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313337"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Kurz: integrace adaptivních přehledů s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat adaptivní přehledy s Azure Active Directory (Azure AD). Když integrujete adaptivní přehledy pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k adaptivním přehledům.
* Umožněte uživatelům, aby se automaticky přihlásili k adaptivním přehledům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s jednotným přihlašováním (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adaptivní přehledy podporují jednotné přihlašování (SSO) iniciované **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Přidávání adaptivních přehledů z Galerie

Pokud chcete nakonfigurovat integraci adaptivních přehledů do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat adaptivní přehledy z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **adaptivní přehledy** .
1. Na panelu výsledků vyberte **adaptivní přehledy** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí adaptivních přehledů s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v adaptivním Insights.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí adaptivních přehledů, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Konfigurace jednotného přihlašování k adaptivním přehledům](#configure-adaptive-insights-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele s adaptivním přehledem](#create-adaptive-insights-test-user)** , abyste měli protějšek B. Simon v adaptivních přehledech, které jsou propojené s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **adaptivních přehledů** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Na stránce **nastavení jednotného přihlašování SAML** pro adaptivní přehledy můžete získat identifikátor (ID entity) a adresu URL odpovědi.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení adaptivních přehledů** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Konfigurace jednotného přihlašování pro adaptivní přehledy

1. V jiném okně webového prohlížeče se přihlaste k firemní síti s adaptivním přehledem jako správce.

2. Přejít na **Správa**.

    ![Snímek obrazovky, který zvýrazní správu na navigačním panelu.](./media/adaptivesuite-tutorial/ic805644.png "Správce")

3. V části **Uživatelé a role** klikněte na **Nastavení SAML SSO**.

    ![Správa nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/ic805645.png "Správa nastavení jednotného přihlašování SAML")

4. Na stránce **nastavení jednotného přihlašování SAML** proveďte následující kroky:

    ![Nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/ic805646.png "Nastavení jednotného přihlašování SAML")

    a. Do textového pole **název zprostředkovatele identity** zadejte název konfigurace.

    b. Vložte hodnotu **identifikátoru Azure AD** zkopírovanou z Azure Portal do TEXTOVÉHO pole **ID entity zprostředkovatele identity** .

    c. Vložte hodnotu **URL pro přihlášení** zkopírovanou z Azure Portal do textového pole **adresy URL jednotného přihlašování zprostředkovatele identity** .

    d. Vložte hodnotu **URL pro odhlášení** zkopírovanou z Azure Portal do pole **vlastní adresa URL pro odhlášení** .

    e. Pokud chcete nahrát stažený certifikát, klikněte na **zvolit soubor**.

    f. Vyberte následující:

     * **ID uživatele SAML**, vyberte uživatelské **jméno adaptivního Insights uživatele**.

     * **Místo ID uživatele SAML**vyberte **ID uživatele v NameId předmětu**.

     * Ve **formátu SAML NameId**vyberte **e-mailová adresa**.

     * **Povolte SAML**, vyberte možnost **Povolit jednotné přihlašování SAML a přímé přihlašování s adaptivním přehledem**.

    například Zkopírujte **adresu URL jednotného Insights SSO** a vložte ji do pole **identifikátor (ID entity)** a text **adresy URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    h. Klikněte na **Uložit**.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k adaptivním přehledům.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **adaptivní přehledy**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-adaptive-insights-test-user"></a>Vytvořit testovacího uživatele adaptivních přehledů

Aby se uživatelé Azure AD mohli přihlašovat do adaptivních přehledů, musí se zřídit do adaptivních přehledů. V případě adaptivních přehledů je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k firemní síti s **adaptivním přehledem** jako správce.

2. Přejít na **Správa**.

   ![Správce](./media/adaptivesuite-tutorial/IC805644.png "Správce")

3. V části **Uživatelé a role** klikněte na **Uživatelé**.

   ![Přidat uživatele](./media/adaptivesuite-tutorial/IC805648.png "Přidání uživatele")

4. V části **Nový uživatel** proveďte následující kroky:

   ![Odeslat](./media/adaptivesuite-tutorial/IC805649.png "Odeslat")

   a. Do příslušných textových polí zadejte **jméno**, **uživatelské jméno**, **e-mail**, **heslo** platného Azure Active Directoryho uživatele, kterého chcete zřídit.

   b. Vyberte **roli**.

   c. Klikněte na **Odeslat**.

> [!NOTE]
> K zajištění uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů a rozhraní API pro adaptivní přehledy, které poskytuje adaptivní přehledy.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici adaptivní přehledy na přístupovém panelu byste měli být automaticky přihlášení do adaptivních přehledů, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)