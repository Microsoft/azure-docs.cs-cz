---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SumoLogic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521889"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SumoLogic

V tomto kurzu se dozvíte, jak integrovat SumoLogic s Azure Active Directory (Azure AD). Když integrujete SumoLogic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SumoLogic.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SumoLogic svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SumoLogic odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SumoLogic podporuje jednotné přihlašování **IDP** .

## <a name="adding-sumologic-from-the-gallery"></a>Přidání SumoLogic z Galerie

Pokud chcete nakonfigurovat integraci SumoLogic do služby Azure AD, musíte přidat SumoLogic z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SumoLogic** .
1. Na panelu výsledků vyberte **SumoLogic** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SumoLogic

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SumoLogic pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SumoLogic.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SumoLogic, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SUMOLOGIC SSO](#configure-sumologic-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte SumoLogic Test User](#create-sumologic-test-user)** -to, abyste měli protějšek B. Simon v SumoLogic, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SumoLogic** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta SumoLogic](https://www.sumologic.com/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. SumoLogic aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace SumoLogic několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    |  Name | Zdrojový atribut |
    | ---------------| --------------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | Role | User. assignedroles |

    > [!NOTE]
    > Pokud chcete zjistit, jak nakonfigurovat **roli** v Azure AD, klikněte prosím [sem](../develop/active-directory-enterprise-app-role-management.md) .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení SumoLogic** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SumoLogic.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SumoLogic**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sumologic-sso"></a>Konfigurace jednotného přihlašování SumoLogic

1. V jiném okně webového prohlížeče se přihlaste k webu SumoLogic společnosti jako správce.

1. Přejít na **Správa \> zabezpečení**

    ![Spravovat](./media/sumologic-tutorial/ic778556.png "Spravovat")

1. Klikněte na **SAML**.

    ![Globální nastavení zabezpečení](./media/sumologic-tutorial/ic778557.png "Globální nastavení zabezpečení")

1. V seznamu **Vybrat konfiguraci nebo vytvořit nový** vyberte **Azure AD** a pak klikněte na **Konfigurovat**.

    ![Snímek obrazovky zobrazuje konfiguraci SAML 2,0, kde můžete vybrat Azure A D.](./media/sumologic-tutorial/ic778558.png "Konfigurace SAML 2,0")

1. V dialogovém okně **Konfigurace SAML 2,0** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí dialogové okno Konfigurace SAML 2,0, kde můžete zadat hodnoty, které jsou popsány.](./media/sumologic-tutorial/ic778559.png "Konfigurace SAML 2,0")

    a. Do textového pole **název konfigurace** zadejte **Azure AD**.

    b. Vyberte **režim ladění**.

    c. Do textového pole **vystavitele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL požadavku Authn** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    e. V programu Poznámkový blok otevřete svůj certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **certifikátu X. 509** .

    f. Jako **atribut e-mailu** vyberte **použít předmět SAML**.  

    například Vyberte **Konfigurace přihlášení iniciovaná pomocí SP**.

    h. Do textového pole **cesta přihlášení** zadejte **Azure** a klikněte na **Uložit**.

### <a name="create-sumologic-test-user"></a>Vytvořit testovacího uživatele SumoLogic

Aby se uživatelé Azure AD mohli přihlašovat k SumoLogic, musí se zřídit až SumoLogic. V případě SumoLogic je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **SumoLogic** .

1. Přejít na **Správa \> uživatelů**

    ![Snímek obrazovky zobrazuje uživatele vybrané v nabídce spravovat.](./media/sumologic-tutorial/ic778561.png "Uživatelé")

1. Klikněte na **Přidat**.

    ![Snímek obrazovky se zobrazí tlačítko Přidat pro uživatele.](./media/sumologic-tutorial/ic778562.png "Uživatelé")

1. V dialogovém okně **Nový uživatel** proveďte následující kroky:

    ![Nový uživatel](./media/sumologic-tutorial/ic778563.png "Nový uživatel")

    a. Zadejte související informace o účtu Azure AD, který chcete zřídit, do textových polí **jméno**, **příjmení** a **e-mailová** pole.
  
    b. Vyberte roli.
  
    c. Jako **stav** vyberte **aktivní**.
  
    d. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů SumoLogic nebo rozhraní API poskytovaná SumoLogic.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SumoLogic, měli byste se automaticky přihlásit k SumoLogic, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si SumoLogic s Azure AD](https://aad.portal.azure.com/)