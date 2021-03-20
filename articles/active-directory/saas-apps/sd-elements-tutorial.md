---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s elementy SD | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi prvky Azure Active Directory a SD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893589"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s elementy SD

V tomto kurzu se dozvíte, jak integrovat prvky SD pomocí Azure Active Directory (Azure AD). Když integrujete prvky SD s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k elementům SD.
* Umožněte uživatelům, aby se automaticky přihlásili k elementům SD pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Prvky SD s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Elementy SD podporují jednotné přihlašování (SSO) iniciované **IDP**

## <a name="adding-sd-elements-from-the-gallery"></a>Přidání elementů SD z Galerie

Chcete-li nakonfigurovat integraci prvků SD do služby Azure AD, je nutné přidat prvky SD z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **elementy SD** .
1. Vyberte **prvky SD** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Konfigurace a testování jednotného přihlašování Azure AD pro elementy SD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s elementy SD pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v prvcích SD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s elementy SD, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování pro prvky SD](#configure-sd-elements-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořit prvky SD – testovací uživatel](#create-sd-elements-test-user)** – Pokud chcete mít protějšek B. Simon v elementech SD, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **prvky SD** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory elementu SD](mailto:support@sdelements.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace elementů SD očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše je aplikace prvků SD očekává, že se v odpovědi SAML vrátí zpět několik atributů, které jsou uvedeny níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | --- | --- |
    | e-mail |uživatel. pošta |
    | FirstName |User. křestní jméno |
    | polím |User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit elementy SD** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k elementům SD.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **prvky SD**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sd-elements-sso"></a>Nakonfigurovat jednotné přihlašování pro prvky SD

1. Pokud chcete získat jednotné přihlašování, obraťte se na [tým podpory prvků SD](mailto:support@sdelements.com) a poskytněte mu stažený soubor certifikátu.

1. V jiném okně prohlížeče se přihlaste ke svému tenantovi elementy SD jako správce.

1. V nabídce v horní části klikněte na **systém** a pak na **jednotné přihlašování**.

    ![Snímek obrazovky s vybraným možnostem "System" a "jednotné přihlašování" v rozevíracím seznamu.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. V dialogovém okně **Nastavení jednoho Sign-On** proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Jako **Typ jednotného přihlašování** vyberte **SAML**.

    b. Do textového pole **ID entity zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **služba poskytovatele identity s jednou Sign-On službou** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    d. Klikněte na **Uložit**.

### <a name="create-sd-elements-test-user"></a>Vytvořit testovacího uživatele pro elementy SD

Cílem této části je vytvořit uživatele s názvem B. Simon v prvcích SD. V případě elementů SD je vytváření elementů SD ručním úkolem.

**Chcete-li vytvořit B. Simon v prvcích SD, proveďte následující kroky:**

1. V okně webového prohlížeče se přihlaste k vašim prvkům SD elementy SD jako správce.

1. V nabídce v horní části klikněte na **Správa uživatelů** a pak na **Uživatelé**.

    ![Snímek obrazovky, který zobrazuje uživatele vybrané v rozevíracím seznamu Správa uživatelů](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klikněte na tlačítko **Přidat nového uživatele**.

    ![Snímek obrazovky, který zobrazuje vybrané tlačítko Přidat nového uživatele.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. V dialogovém okně **Přidat nového uživatele** proveďte následující kroky:

    ![Vytvoření testovacího uživatele pro elementy SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **b.simon@contoso.com** .

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B.**.

    c. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    d. Jako **role** vyberte **uživatel**.

    e. Klikněte na **vytvořit uživatele**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici prvky SD na přístupovém panelu byste měli být automaticky přihlášeni k prvkům SD, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte elementy SD pomocí Azure AD](https://aad.portal.azure.com/)