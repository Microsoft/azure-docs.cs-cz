---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s cukrem CRM | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cukrem CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: cd1be43bd8984df8fef690e595ec99f4b112c3c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88533615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s cukrem v cukru CRM

V tomto kurzu se dozvíte, jak integrovat cukrovou aplikaci CRM pomocí Azure Active Directory (Azure AD). Když integrujete cukrovou aplikaci CRM s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k cukru CRM
* Umožněte uživatelům, aby se automaticky přihlásili k cukru CRM pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr povolený jednotného přihlašování (SSO) v cukru CRM.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cukrová aplikace **CRM podporuje jednotné** přihlašování (SSO)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-sugar-crm-from-the-gallery"></a>Přidání cukru CRM z Galerie

Pokud chcete nakonfigurovat integraci cukru CRM do Azure AD, musíte přidat do seznamu spravovaných aplikací pro SaaS cukr CRM z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **cukr CRM** .
1. Z panelu výsledků vyberte **cukr CRM** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Konfigurace a testování jednotného přihlašování Azure AD pro cukr CRM

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s cukrem CRM pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cukru CRM.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby cukr CRM, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte si jednotné PŘIhlašování pro cukr CRM](#configure-sugar-crm-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si testovacího uživatele pro cukr CRM](#create-sugar-crm-test-user)** , abyste měli protějšek B. Simon v cukru CRM, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce věnované integraci aplikací **CRM pro cukr** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečnou adresou URL Sign-On a adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta podpory pro cukr CRM](https://support.sugarcrm.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit cukr CRM** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k cukru CRM.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **cukr CRM**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sugar-crm-sso"></a>Konfigurace jednotného přihlašování pro cukr CRM

1. V jiném okně webového prohlížeče se přihlaste k webu cukr CRM společnosti jako správce.

1. Přejít na **správce**.

    ![Správce](./media/sugarcrm-tutorial/ic795888.png "Správce")

1. V části **Správa** klikněte na **Správa hesel**.

    ![Správa](./media/sugarcrm-tutorial/ic795889.png "Správa")

1. Vyberte **Povolit ověřování SAML**.

    ![Správa](./media/sugarcrm-tutorial/ic795890.png "Správa")

1. V části **ověřování SAML** proveďte následující kroky:

    ![Ověřování SAML](./media/sugarcrm-tutorial/ic795891.png "Ověřování SAML")  

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.
  
    b. Do textového pole **Adresa URL objektu slo** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.
  
    c. V programu Poznámkový blok otevřete svůj certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **certifikátu X. 509** .
  
    d. Klikněte na **Uložit**.

### <a name="create-sugar-crm-test-user"></a>Vytvořit testovacího uživatele pro cukr CRM

Aby se uživatelé Azure AD mohli přihlásit k cukru CRM, musí se zřídit do cukru CRM. V případě cukru CRM je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu **cukr CRM** společnosti jako správce.

1. Přejít na **správce**.

    ![Správce](./media/sugarcrm-tutorial/ic795888.png "Správce")

1. V části **Správa** klikněte na **Správa uživatelů**.

    ![Správa](./media/sugarcrm-tutorial/ic795893.png "Správa")

1. Přejít na **uživatele \> Vytvoření nového uživatele**.

    ![Vytvořit nového uživatele](./media/sugarcrm-tutorial/ic795894.png "Vytvořit nového uživatele")

1. Na kartě **Profil uživatele** proveďte následující kroky:

    ![Nový uživatel](./media/sugarcrm-tutorial/ic795895.png "Nový uživatel")

    * Do souvisejících textových polí zadejte **uživatelské jméno**, **příjmení**a **e-mailovou adresu** platného Azure Active Directory uživatele.
  
1. Jako **stav**vyberte **aktivní**.

1. Na kartě heslo proveďte následující kroky:

    ![Nový uživatel](./media/sugarcrm-tutorial/ic795896.png "Nový uživatel")

    a. Do příslušného textového pole zadejte heslo.

    b. Klikněte na **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytvoření uživatelského účtu nebo rozhraní API v cukru CRM poskytované cukrem CRM.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici cukru CRM na přístupovém panelu byste měli být automaticky přihlášeni k cukru CRM, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si cukr CRM s Azure AD](https://aad.portal.azure.com/)

