---
title: 'Kurz: Integrace Azure Active Directory s e-mailem SkyDesk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SkyDesk e-mailu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e0c2dc6c370e697f896e24e7d56c6eb8900601a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867168"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Kurz: Integrace Azure Active Directory s SkyDesk e-mailu

V tomto kurzu se dozvíte, jak integrovat SkyDesk e-mailu s Azure Active Directory (Azure AD).
Integrace SkyDesk e-mailu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k e-mailu SkyDesk.
* Uživatelům se automaticky přihlášeni k e-mailu SkyDesk (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SkyDesk e-mailu, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* E-mailu SkyDesk jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje e-mailu SkyDesk **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-skydesk-email-from-the-gallery"></a>Přidání e-mailu SkyDesk z Galerie

Konfigurace integrace SkyDesk e-mailu do služby Azure AD, budete muset přidat SkyDesk e-mailu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SkyDesk e-mailu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **e-mailu SkyDesk**vyberte **SkyDesk e-mailu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![E-mailu SkyDesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s e-mailem SkyDesk podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské SkyDesk e-mailem.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SkyDesk e-mailu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SkyDesk e-mailu Single Sign-On](#configure-skydesk-email-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele e-mailu SkyDesk](#create-skydesk-email-test-user)**  – Pokud chcete mít protějšek Britta Simon v e-mailu SkyDesk, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s SkyDesk e-mailem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **e-mailu SkyDesk** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SkyDesk e-mailová doména a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory SkyDesk e-mailovém klientovi](https://www.skydesk.jp/apps/support/) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení e-mailu SkyDesk** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurace e-mailu SkyDesk jednotného přihlašování

1. V různých webový prohlížeč přihlašování k vašemu účtu e-mailu SkyDesk jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení**a vyberte **organizace**.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klikněte na **domén** na levém panelu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klikněte na **přidání domény**.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Zadejte název domény a pak ověřte doménu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klikněte na **ověřování SAML** na levém panelu.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na **ověřování SAML** dialogového okna stránky, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Použití ověřování na základě SAML, měli byste buď mít **ověřené domény** nebo **portál URL** instalační program. Na portálu můžete nastavit adresu URL s jedinečným názvem.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. V **odhlášení** textového pole URL vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. **Změňte heslo URL** je volitelný takže políčko nechte prázdné.

    d. Klikněte na **získáte klíč ze souboru** vyberte svůj certifikát stažený z webu Azure portal a potom klikněte na tlačítko **otevřít** na kterou certifikát nahrajete.

    e. Jako **algoritmus**vyberte **RSA**.

    f. Klikněte na tlačítko **Ok** a uložte změny.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k e-mailu SkyDesk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **e-mailu SkyDesk**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **e-mailu SkyDesk**.

    ![Odkaz e-mailem SkyDesk v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-skydesk-email-test-user"></a>Vytvořit e-mailu SkyDesk testovacího uživatele

V této části vytvořte uživatele Britta Simon SkyDesk e-mailem.

Klikněte na **přístup uživatelů** z levé strany panelu SkyDesk e-mailem a pak zadejte svoje uživatelské jméno.

![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Pokud je potřeba vytvořit uživatele hromadné, budete muset požádat [tým podpory SkyDesk e-mailovém klientovi](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SkyDesk e-mailu na přístupovém panelu, vám by měl být automaticky přihlášeni k SkyDesk e-mailu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

