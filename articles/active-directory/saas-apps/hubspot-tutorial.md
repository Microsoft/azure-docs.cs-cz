---
title: 'Kurz: Integrace Azure Active Directory s HubSpot | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100897"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Kurz: Integrace Azure Active Directory s HubSpot

V tomto kurzu se dozvíte, jak integrovat HubSpot s Azure Active Directory (Azure AD).

HubSpot integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit, kdo má přístup k HubSpot Azure AD.
* Uživatelé mohou být automaticky přihlášeni ke HubSpot pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HubSpot, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure AD, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* HubSpot předplatné s single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a HubSpot integrace s Azure AD.

HubSpot podporuje následující funkce:

* **Iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování**
* **Zahájené pomocí IDP jednotného přihlašování**

## <a name="add-hubspot-in-the-azure-portal"></a>Přidat HubSpot na webu Azure Portal

HubSpot integrovat Azure AD, je nutné přidat HubSpot na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **HubSpot**. Ve výsledcích hledání vyberte **HubSpot**a pak vyberte **přidat**.

    ![HubSpot v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s HubSpot podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit v HubSpot propojené vztah mezi uživatele služby Azure AD a související uživatel.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HubSpot, je nutné dokončit následující stavebních bloků:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům tuto funkci používat. |
| **[Konfigurace HubSpot jednotného přihlašování](#configure-hubspot-single-sign-on)** | Konfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Zkoušky Azure AD jednotného přihlašování pro uživatele s názvem Britta Simon. |
| **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotného přihlašování. |
| **[Vytvoření zkušebního uživatele HubSpot](#create-a-hubspot-test-user)** | Vytvoří protějšek Britta Simon HubSpot, který je propojený s Azure AD zastoupení uživatele. |
| **[Otestovat jednotné přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části nakonfigurujete služby Azure AD jednotné přihlašování s HubSpot na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)v **HubSpot** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Nakonfigurujte možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML** nebo **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokně nakonfigurovat *zahájené pomocí IDP režimu*, proveďte následující kroky:

    1. V **identifikátor** zadejte adresu URL, která má následující vzor: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<ID zákazníka\>.

    1. V **adresy URL odpovědi** zadejte adresu URL, která má následující vzor: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<ID zákazníka\>.

    ![HubSpot domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    > [!NOTE]
    > K formátování adresy URL, najdete také na tyto vzory se dají ukazuje **základní konfiguraci SAML** podokně webu Azure Portal.

1. Konfigurace aplikace v *iniciovaného Zprostředkovatelem přihlašování* režimu:

    1. Vyberte **nastavit další adresy URL**.

    1. V **přihlašovací adresa URL** zadejte **https:\//app.hubspot.com/login**.

    ![Další adresy URL možnost Set](common/metadata-upload-additional-signon.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** vedle **certifikát (Base64)** . Možnost stažení na základě vašich požadavků. Uložte si certifikát ve vašem počítači.

    ![Možnost stažení certifikát (Base64)](common/certificatebase64.png)

1. V **nastavení HubSpot** tématu, zkopírujte následující adresy URL na základě vašich požadavků:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Adresa URL – odhlášení

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurace HubSpot jednotného přihlašování

1. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce HubSpot.

1. Vyberte **nastavení** ikonu v pravém horním rohu stránky.

    ![Ikona nastavení v HubSpot](./media/hubspot-tutorial/config1.png)

1. Vyberte **účtu výchozí**.

    ![Možnost výchozí nastavení účtu v HubSpot](./media/hubspot-tutorial/config2.png)

1. Přejděte dolů k položce **zabezpečení** a potom vyberte **nastavit**.

    ![Nastavte možnost v HubSpot](./media/hubspot-tutorial/config3.png)

1. V **nastavit jednotné přihlašování** části, proveďte následující kroky:

    1. V **URl cílové skupiny (ID Entity poskytovatele služeb)** vyberte **kopírování** zkopírujte hodnotu. Na webu Azure Portal v **základní konfiguraci SAML** podokno, vložte tuto hodnotu v **identifikátor** pole.

    1. V **přihlásit na adrese URl, ACS, příjemce nebo přesměrovat** vyberte **kopírování** zkopírujte hodnotu. Na webu Azure Portal v **základní konfiguraci SAML** podokno, vložte tuto hodnotu v **adresy URL odpovědi** pole.

    1. V HubSpot v **identifikátor zprostředkovatele Identity nebo URL vystavitele** pole, vložte tuto hodnotu pro **Azure AD identifikátor** , který jste zkopírovali na webu Azure Portal.

    1. V HubSpot v **jednotné přihlašování – adresa URL zprostředkovatele Identity** pole, vložte tuto hodnotu pro **přihlašovací adresa URL** , který jste zkopírovali na webu Azure Portal.

    1. V poznámkovém bloku Windows otevřete stažený soubor Certificate(Base64). Vyberte a zkopírujte obsah souboru. V HubSpot, vložte ji **certifikát X.509** pole.

    1. Vyberte **ověřte**.

        ![Nastavit jednotné přihlašování v části HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechny možnosti uživatele](common/users.png)

1. Vyberte **nového uživatele**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **brittasimon\@\<your domény společnosti >.\< rozšíření\>** . Například **brittasimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

    ![V podokně uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte přístup Britta Simon k HubSpot, které můžete použít Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **HubSpot**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **HubSpot**.

    ![HubSpot v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v seznamu uživatelů. Zvolte **Vybrat**.

1. Pokud se očekává hodnotu kontrolního výrazu SAML, do role v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-hubspot-test-user"></a>Vytvoření zkušebního uživatele HubSpot

Povolit uživateli umožní přihlásit k HubSpot, uživatel musí být zřízený v HubSpot služby Azure AD. Zřizování v HubSpot, je ruční úlohy.

K poskytnutí uživatelského účtu v HubSpot:

1. Přihlaste se na web společnosti HubSpot jako správce.

1. Vyberte **nastavení** ikonu v pravém horním rohu stránky.

    ![Ikona nastavení v HubSpot](./media/hubspot-tutorial/config1.png)

1. Vyberte **uživatelé a týmy**.

    ![Možnost uživatelé a týmy v HubSpot](./media/hubspot-tutorial/user1.png)

1. Vyberte **vytvořit uživatele**.

    ![Možnost vytvořit uživatele v HubSpot](./media/hubspot-tutorial/user2.png)

1. V **přidat e-mailu addess(es)** okně zadejte e-mailovou adresu uživatele ve formátu brittasimon\@contoso.com a pak vyberte **Další**.

    ![Přidat e-mailové adresy pole v části vytvořit uživatele v HubSpot](./media/hubspot-tutorial/user3.png)

1. V **vytvořit uživatele** vyberte každé kartě. Na každé kartě nastavte příslušné možnosti a oprávnění pro uživatele. Pak vyberte **Next** (Další).

    ![Karty v části vytvořit uživatele v HubSpot](./media/hubspot-tutorial/user4.png)

1. Chcete-li odeslat pozvánku uživateli, vyberte **odeslat**.

    ![Možnost odeslat v HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Uživatel se aktivuje, jakmile uživatel přijme podmínky e-mailové pozvánce.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu Moje aplikace.

Jakmile nastavíte jednotné přihlašování, když vyberete **HubSpot** na portálu Moje aplikace, budete automaticky přihlášeni k HubSpot. Další informace o portálu Moje aplikace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další postup

Další informace, projděte si tyto články:

- [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
