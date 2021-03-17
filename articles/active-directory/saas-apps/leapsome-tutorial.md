---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Leapsome | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ddc8cce7b56e0d9d4b3dc33dc1ad2d8c16582841
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Leapsome

V tomto kurzu se dozvíte, jak integrovat Leapsome s Azure Active Directory (Azure AD). Když integrujete Leapsome s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Leapsome.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Leapsome svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Leapsome odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Leapsome podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-leapsome-from-the-gallery"></a>Přidání Leapsome z Galerie

Pokud chcete nakonfigurovat integraci Leapsome do služby Azure AD, musíte přidat Leapsome z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Leapsome** .
1. Na panelu výsledků vyberte **Leapsome** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Leapsome

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Leapsome pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Leapsome.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Leapsome, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte LEAPSOME SSO](#configure-leapsome-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Leapsome Test User](#create-leapsome-test-user)** -to, abyste měli protějšek B. Simon v Leapsome, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Leapsome** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://www.leapsome.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Hodnota adresy URL předchozí odpovědi a přihlašovací adresy URL nejsou reálné hodnoty. Ty budete aktualizovat o skutečné hodnoty, které jsou vysvětleny dále v tomto kurzu.

1. Leapsome aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Leapsome několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut | Obor názvů |
    | ---------------| --------------- | --------- |  
    | FirstName | User. křestní jméno | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | polím | User. příjmení | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | User. jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | obrázk | Adresa URL obrázku zaměstnance | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Hodnota atributu Picture není typu Real. Aktualizuje tuto hodnotu skutečnou adresou URL obrázku. Pokud chcete tuto hodnotu získat, obraťte se na [tým podpory Leapsome Client](mailto:support@leapsome.com).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Leapsome** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Leapsome.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Leapsome**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-leapsome-sso"></a>Konfigurace jednotného přihlašování Leapsome

1. V jiném okně webového prohlížeče se přihlaste k Leapsome jako správce zabezpečení.

1. V pravém horním rohu klikněte na logo nastavení a potom klikněte na **Nastavení správce**.

    ![Leapsome sada](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. V levém řádku nabídek klikněte na **jednotné přihlašování (SSO)** a na stránce **jednotného přihlašování založeného na SAML** proveďte následující kroky:

    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Vyberte možnost **Povolit jednotné přihlašování založené na SAML**.

    b. Zkopírujte **adresu URL pro přihlášení (tady uveďte uživatele pro spuštění přihlášení)** a vložte ji do textového pole **přihlašovací adresa URL** v základní části **Konfigurace SAML** na Azure Portal.

    c. Zkopírujte **adresu URL odpovědi (přijme odpověď od poskytovatele identity)** a vložte ji do textového pole **Adresa URL odpovědi** v  **základní části Konfigurace SAML** na Azure Portal.

    d. Do textového pole **Adresa URL pro přihlášení SSO (poskytovatel identity)** vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.

    e. Zkopírujte certifikát, který jste si stáhli z Azure Portal bez `--BEGIN CERTIFICATE and END CERTIFICATE--` komentářů a vložte ho do textového pole **certifikát (poskytnutý zprostředkovatelem identity)** .

    f. Klikněte na **aktualizovat nastavení jednotného přihlašování**.

### <a name="create-leapsome-test-user"></a>Vytvořit testovacího uživatele Leapsome

V této části vytvoříte uživatele s názvem Britta Simon v Leapsome. Pokud chcete přidat uživatele nebo doménu, která musí být přidána do seznamu povolených platforem Leapsome, pracujte s [týmem podpory klienta Leapsome](mailto:support@leapsome.com) . Pokud je doména přidána týmem, uživatelé se automaticky zřídí pro platformu Leapsome. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Leapsome, měli byste se automaticky přihlásit k Leapsome, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Leapsome s Azure AD](https://aad.portal.azure.com/)