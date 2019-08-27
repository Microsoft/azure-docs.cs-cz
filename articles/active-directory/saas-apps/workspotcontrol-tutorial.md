---
title: 'Kurz: Integrace Azure Active Directory s řízením Workspot | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování pro služby Azure Active Directory a Workspot ovládacího prvku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086687"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Kurz: Integrace Azure Active Directory s řízením Workspot

V tomto kurzu se dozvíte, jak integrovat ovládací prvek Workspot se službou Azure Active Directory (Azure AD). Při řízení Workspot integraci s Azure AD, můžete:

* Pomocí Azure AD a určovat, kdo má přístup k Workspot ovládacího prvku.
* Povolte uživatelům přihlásit se automaticky Workspot ovládací prvek (jednotné přihlašování [jednotné přihlašování]) pomocí svých účtů služby Azure AD.
* Správa účtů v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [jednotné přihlašování k aplikacím ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ovládacím prvkem Workspot, budete potřebovat následující věci:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

* Ovládací prvek Workspot jednotné přihlašování na povoleno předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

> [!Note]
> Ovládací prvek Workspot podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování a zahájené pomocí IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Přidání ovládacího prvku Workspot z Galerie

Konfigurace integrace Workspot ovládacího prvku do služby Azure AD, musíte přidat ovládací prvek Workspot z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání ovládacího prvku Workspot z galerie, postupujte podle těchto kroků:**

1. V levém podokně [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

3. Vyberte **novou aplikaci** v horní části okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Workspot řízení**vyberte **Workspot řízení** z panel výsledků a pak vyberte **přidat**.

     !["Přidat z Galerie" okna](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ovládacím prvkem Workspot pro testovacího uživatele Britta Simon.
Pro jednotné přihlašování pro práci je potřeba vytvořit propojení mezi uživatele služby Azure AD a související uživatel v ovládacím prvku Workspot.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ovládacím prvkem Workspot, je nutné provést následující úkoly:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům použít funkci.
2. [Konfigurovat ovládací prvek Workspot jednotného přihlašování](#configure-workspot-control-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotného přihlašování pro Britta Simon.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Vytvoření zkušebního uživatele ovládací prvek Workspot](#create-a-workspot-control-test-user) stanovit protějšek Britta Simon Workspot ovládacího prvku, který je propojený s Azure AD zastoupení uživatele.
6. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ovládacím prvkem Workspot, postupujte podle těchto kroků:

1. Na **Workspot řízení** stránky integrace aplikace v [webu Azure portal](https://portal.azure.com/)vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![Vyberte okno jednotné přihlašování – vyberte – metoda](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** (tužka) pro přístup k **základní konfiguraci SAML**.

    ![Upravit ikonu zvýrazněno "Základní konfiguraci SAML"](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IDP, postupujte podle těchto kroků:

    ![Ovládací prvek Workspot domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    1. V **identifikátor** textové pole, zadejte adresu URL v následujícímu vzoru:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. V **adresa URL pro odpověď** textové pole, zadejte adresu URL v následujícímu vzoru:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **nastavit další adresy URL**.

    ![Ovládací prvek Workspot domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL v následujícímu vzoru:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty nahraďte skutečné identifikátor, adresa URL pro odpověď a adresu URL přihlašování. Obraťte se [tým podpory klienta řízení Workspot](mailto:support@workspot.com) k získání těchto hodnot. Nebo můžete také odkazovat na tyto vzory se dají v **základní konfiguraci SAML** části webu Azure portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** Stáhnout **certifikát (Base64)** z dostupných možností podle vašich požadavků. Uložte ho do počítače.

    ![Odkaz ke stažení certifikát (Base64)](common/certificatebase64.png)

7. V **nastavit řízení Workspot** tématu, zkopírujte příslušné adresy URL podle vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    - **Adresa URL pro přihlášení**

    - **Azure AD Identifier**

    - **Odhlašovací adresa URL**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurovat ovládací prvek Workspot jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k řízení Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **nastavení** a potom **SAML**.

    ![Možnosti nastavení](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. V **konfiguraci zabezpečení kontrolního výrazu značky jazyka** okno, postupujte podle těchto kroků:
 
    ![Okno kontrolní výraz Markup Language konfigurace zabezpečení](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. V **Entity ID** pole, vložte **Azure Ad identifikátor** , který jste zkopírovali z portálu Azure portal.

    1. V **adresa URL služby Sign-on** pole, vložte **přihlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.

    1. V **odhlašovací adresa URL služby** pole, vložte **odhlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.

    1. Vyberte **soubor aktualizace** k odeslání do certifikátu X.509 base-64 kódování certifikátu, který jste si stáhli z webu Azure portal.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal.

1. V levém podokně webu Azure portal vyberte **Azure Active Directory**, **uživatelé**a potom **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části okna.

    !["Nový uživatel" tlačítko](common/new-user.png)

3. Ve vlastnostech pro uživatele postupujte podle těchto kroků:

    ![V okně Vlastnosti uživatele](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **brittasimon@* yourcompanydomain.extension***. Zadejte například  **BrittaSimon@contoso.<i> </i>com**.

    1. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište si hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte přístup Britta Simon Workspot ovládací prvek umožňuje ji používat Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**, **všechny aplikace**a potom **ovládací prvek Workspot**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ovládací prvek Workspot**.

    ![Odkaz Workspot ovládací prvek v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** okna.

    ![V okně "Přidat přiřazení"](common/add-assign-user.png)

5. V **uživatelů a skupin** okně **Britta Simon** z **uživatelé** seznamu. Pak klikněte na **Vybrat**.

6. Pokud očekáváte, že všechny role hodnotu v kontrolní výraz SAML, vyberte vhodnou roli pro uživatele v seznamu **vybrat roli** okna. Pak klikněte na tlačítko **vyberte** v dolní části.

7. V **přidat přiřazení** okně **přiřadit**.

### <a name="create-a-workspot-control-test-user"></a>Vytvoření zkušebního uživatele Workspot ovládacího prvku

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Workspot ovládací prvek, musí být zřízená do Workspot ovládacího prvku. Zřizování se ruční úlohy.

**Poskytnutí uživatelského účtu, postupujte podle těchto kroků:**

1. Přihlaste se k řízení Workspot jako správce zabezpečení.

2. Na panelu nástrojů v horní části stránky vyberte **uživatelé** a potom **přidat uživatele**.

    ![Možnosti "Users"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. V **přidat nového uživatele** okno, postupujte podle těchto kroků:

    ![Okno "Přidání nového uživatele."](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. V **křestní jméno** pole, zadejte jméno uživatele, jako například **Britta**.

    1. V **příjmení** textové pole, zadejte příjmení uživatele, jako například **Simon**.

    1. V **e-mailu** okně zadejte e-mailovou adresu uživatele, jako například  **Brittasimon@contoso.<i> </i>com**.

    1. Vyberte příslušné role uživatele z **Role** rozevíracího seznamu.

    1. Vyberte příslušné skupiny uživatelů z **skupiny** rozevíracího seznamu.

    1. Vyberte **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části jsme naše služby Azure AD jednotné přihlašování Konfigurace testu prostřednictvím *přístupového panelu*.

Když kliknete **ovládací prvek Workspot** dlaždici přístupovém panelu, vám by měl být automaticky přihlášeni k Workspot ovládací prvek, u kterého nastavíte jednotné přihlašování. Další informace najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
