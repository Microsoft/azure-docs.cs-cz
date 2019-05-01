---
title: 'Kurz: Integrace s Azure Active Directory se Zscalerem privátní přístup (ZPA) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem privátní přístup (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 3213667e95c1e5cb68a849d6031db9629e5b273b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692672"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Kurz: Integrace s Azure Active Directory se Zscalerem privátní přístup (ZPA)

V tomto kurzu se dozvíte, jak integrovat Zscalerem privátní přístup (ZPA) se službou Azure Active Directory (Azure AD).
Integrace Zscalerem privátní přístup (ZPA) s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscalerem privátní přístup (ZPA).
* Uživatelům se automaticky přihlášeni k Zscalerem privátní přístup (ZPA) (jednotné přihlašování) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem privátní přístup (ZPA), potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Zscalerem privátní přístup (ZPA) jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Zscalerem privátní přístup (ZPA) podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscalerem privátní přístup (ZPA) z Galerie

Konfigurace integrace nástroje Zscalerem privátní přístup (ZPA) do služby Azure AD, budete muset přidat Zscalerem privátní přístup (ZPA) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zscalerem privátní přístup (ZPA) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscalerem privátní přístup (ZPA)** vyberte **Zscalerem privátní přístup (ZPA)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Zscalerem privátní přístup (ZPA) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA) podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem privátní přístup (ZPA).

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Zscalerem privátní přístup (ZPA) Single Sign-On](#configure-zscaler-private-access-zpa-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Zscalerem privátní přístup (ZPA)](#create-zscaler-private-access-zpa-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscalerem privátní přístup (ZPA), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Zscalerem privátní přístup (ZPA)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Zscalerem privátní přístup (ZPA) domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > **Přihlašovací adresa URL** není skutečnou hodnotu. Skutečná adresa URL přihlašování zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Zscalerem privátní přístup (ZPA) klienta](https://help.zscaler.com/zpa-submit-ticket) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit až Zscalerem privátní přístup (ZPA)** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-zscaler-private-access-zpa-single-sign-on"></a>Konfigurace Zscalerem privátní přístup (ZPA) jednotného přihlašování

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Zscalerem privátní přístup (ZPA).

2. Přejděte do **správce** a potom klikněte na tlačítko **konfigurace zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

3. V **konfigurace zprostředkovatele identity** klikněte na tlačítko **přidat nové konfigurace zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

4. V **nové konfigurace zprostředkovatele identity** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Klikněte na tlačítko **vybrat soubor** a nahrát soubor stažený metadat.

    b. Klikněte na tlačítko **Uložit** tlačítko.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem privátní přístup (ZPA).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Zscalerem privátní přístup (ZPA)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Zscalerem privátní přístup (ZPA)**.

    ![Odkaz Zscalerem privátní přístup (ZPA) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Vytvořit testovacího uživatele Zscalerem privátní přístup (ZPA)

V této části vytvoříte uživateli Britta Simon v Zscalerem privátní přístup (ZPA). Spojte se prosím s [tým podpory Zscalerem privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) přidat uživatele na platformě Zscalerem privátní přístup (ZPA).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem privátní přístup (ZPA) na přístupovém panelu, můžete by měl být automaticky přihlášeni k Zscalerem privátní přístup (ZPA) u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

