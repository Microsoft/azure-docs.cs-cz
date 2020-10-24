---
title: 'Kurz: Azure Active Directory integrace s PagerDuty | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: ec3bcda7953d3b5cb1cb1dae1feebe1da4a9fb04
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92513360"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PagerDuty

V tomto kurzu se dozvíte, jak integrovat PagerDuty s Azure Active Directory (Azure AD). Když integrujete PagerDuty s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k PagerDuty.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k PagerDuty svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* PagerDuty odběr s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Pokud se službou Azure AD používáte ověřování MFA nebo bez hesla, pak v žádosti SAML přepněte hodnotu AuthnContext. V opačném případě Azure AD vyvolá chybu na neshodu AuthnContext a nepošle token zpátky do aplikace.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PagerDuty podporuje jednotné přihlašování iniciované v **SP**
* Po nakonfigurování PagerDuty můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Přidání PagerDuty z Galerie

Pokud chcete nakonfigurovat integraci PagerDuty do služby Azure AD, musíte přidat PagerDuty z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PagerDuty** .
1. Na panelu výsledků vyberte **PagerDuty** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Konfigurace a testování jednotného přihlašování Azure AD pro PagerDuty

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí PagerDuty pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PagerDuty.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí PagerDuty, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte PAGERDUTY SSO](#configure-pagerduty-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte PagerDuty Test User](#create-pagerduty-test-user)** -to, abyste měli protějšek B. Simon v PagerDuty, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **PagerDuty** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.pagerduty.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.pagerduty.com`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta PagerDuty](https://www.pagerduty.com/support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení PagerDuty** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k PagerDuty.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **PagerDuty**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-pagerduty-sso"></a>Konfigurace jednotného přihlašování PagerDuty

1. V jiném okně webového prohlížeče se přihlaste k webu PagerDuty společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení účtu**.

    ![Nastavení účtu](./media/pagerduty-tutorial/ic778535.png "Nastavení účtu")

3. Klikněte na **jednotné přihlašování**.

    ![Jednotné přihlašování](./media/pagerduty-tutorial/ic778536.png "Jednotné přihlašování")

4. Na stránce **Povolit jednotné přihlašování (SSO)** proveďte následující kroky:

    ![Povolit jednotné přihlašování](./media/pagerduty-tutorial/ic778537.png "Povolit jednotné přihlašování")

    a. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu X. 509.**
  
    b. Do textového pole **Adresa URL pro přihlášení** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.
  
    c. V textovém poli **Adresa URL pro odhlášení** vložte **adresu URL** pro odhlášení, kterou jste zkopírovali z Azure Portal.

    d. Vyberte možnost **Povolení přihlášení pomocí uživatelského jména a hesla**.

    e. Zaškrtněte políčko **vyžadovat přesné ověření kontextu ověřování** .

    f. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-pagerduty-test-user"></a>Vytvořit testovacího uživatele PagerDuty

Aby se uživatelé Azure AD mohli přihlašovat k PagerDuty, musí se zřídit v PagerDuty. V případě PagerDuty je zřizování ručním úkolem.

> [!NOTE]
> K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů PagerDuty nebo rozhraní API poskytovaná PagerDuty.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k **PagerDuty** tenantovi.

2. V nabídce v horní části klikněte na **Uživatelé**.

3. Klikněte na **Přidat uživatele**.
   
    ![Přidat uživatele](./media/pagerduty-tutorial/ic778539.png "Přidat uživatele")

4.  V dialogovém okně **pozvání týmu** proveďte následující kroky:
   
    ![Pozvání týmu](./media/pagerduty-tutorial/ic778540.png "Pozvání týmu")

    a. Zadejte **jméno a příjmení uživatele,** jako je **B. Simon**. 
   
    b. Zadejte **e-mailovou** adresu uživatele, jako je **b. Simon \@ contoso.com**.
   
    c. Klikněte na **Přidat**a pak klikněte na **Odeslat pozvánky**.
   
    > [!NOTE]
    > Všichni přidaní uživatelé budou dostávat pozvánku k vytvoření účtu PagerDuty.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici PagerDuty, měli byste se automaticky přihlásit k PagerDuty, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si PagerDuty s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit PagerDuty pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)