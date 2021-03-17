---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu LinkedIn Sales Navigator | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedIn Sales Navigatoru.
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
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím LinkedIn Sales Navigatoru

V tomto kurzu se dozvíte, jak integrovat obchodní navigátor LinkedInu pomocí Azure Active Directory (Azure AD). Když integrujete aplikaci LinkedIn Sales Navigator s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k aplikaci LinkedIn Sales Navigator.
* Umožněte uživatelům, aby se automaticky přihlásili k aplikaci LinkedIn Sales Navigator pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné LinkedIn Sales Navigator jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LinkedIn Sales Navigator podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* LinkedIn Sales Navigator podporuje zřizování uživatelů **jenom v čase**
* LinkedIn Sales Navigator podporuje [ **automatizované** zřizování uživatelů](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Přidání obchodní navigátoru LinkedIn z Galerie

Pokud chcete nakonfigurovat integraci aplikace LinkedIn Sales Navigator do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie LinkedIn Sales Navigator.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **LinkedIn Sales Navigator** .
1. Z panelu výsledků vyberte **LinkedIn Sales Navigator** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Konfigurace a testování jednotného přihlašování Azure AD pro LinkedIn Sales Navigator

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí LinkedIn Sales Navigatoru pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v aplikaci LinkedIn Sales Navigator.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LinkedIn Sales Navigator, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování pro LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořit testovacího uživatele pro LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** – Pokud chcete mít protějšek B. Simon ve službě LinkedIn Sales Navigator, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace pro **LinkedIn Sales Navigator** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **Identifier (identifikátor** ) zadejte hodnotu **ID entity** , kterou zkopírujete z portálu LinkedIn, který se vysvětluje dále v tomto kurzu.

    b. Do textového pole **Adresa URL odpovědi** zadejte hodnotu **adresy URL pro řízení přístupu uživatele (ACS)** . na portálu LinkedIn je vysvětleno později v tomto kurzu.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Aplikace LinkedIn Sales Navigator očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace LinkedIn Sales Navigator několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | --- | --- |
    | e-mail| uživatel. pošta |
    | Oddělení| User. Department |
    | FirstName| User. křestní jméno |
    | polím| User. příjmení |
    | Jedinečný identifikátor uživatele | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit obchodní navigátor LinkedIn** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k prodejnímu navigátoru LinkedIn.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **LinkedIn Sales Navigator**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-linkedin-sales-navigator-sso"></a>Konfigurace jednotného přihlašování pro LinkedIn Sales Navigator

1. V jiném okně webového prohlížeče se přihlaste k webu **LinkedIn Sales Navigator** jako správce.

1. V **centru účtů**klikněte na **globální nastavení** v části **Nastavení**. V rozevíracím seznamu také vyberte možnost **obchodní navigátor** .

    ![Snímek obrazovky zobrazuje nastavení aplikace, kde můžete vybrat obchodní navigátor.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknutím **nebo kliknutím sem načtěte a zkopírujte jednotlivá pole z formuláře** a proveďte následující kroky:

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Zkopírujte **ID entity** a vložte ho do textového pole **identifikátor** v **základní konfiguraci SAML** v Azure Portal.

    b. Zkopírujte **adresu URL pro přístup uživatelů kontrolního výrazu (ACS)** a vložte ji do textového pole **Adresa URL odpovědi** v **základní konfiguraci SAML** v Azure Portal.

1. Přejít na část **Nastavení správce LinkedIn** . Kliknutím na možnost **nahrát soubor XML** nahrajte soubor XML, který jste stáhli z Azure Portal.

    ![Snímek obrazovky ukazuje konfiguraci nastavení poskytovatele služby LinkedIn Service S příponami v, kde můžete nahrát soubor X M L.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknutím **na** povolíte jednotné přihlašování. Změna stavu jednotného přihlašování z **nepřipojeného** k **připojenému**

    ![Snímek obrazovky zobrazuje jednu Sign-On, kde můžete povolit ověřování uživatelů pomocí s S S S.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Vytvořit testovacího uživatele pro LinkedIn Sales Navigator

Propojená aplikace obchodní navigátor podporuje zřizování uživatelů just in time (JIT) a po ověření, že se uživatelé v aplikaci automaticky vytvoří. Aktivací **automatické přiřazování licencí** přiřadíte licenci uživateli.

   ![Vytvoření testovacího uživatele Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici LinkedIn Sales Navigator na přístupovém panelu byste měli být automaticky přihlášení k prodejnímu navigátoru LinkedIn, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si LinkedIn Sales Navigator s Azure AD](https://aad.portal.azure.com/)