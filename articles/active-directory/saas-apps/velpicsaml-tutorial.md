---
title: 'Kurz: Integrace Azure Active Directory s Velpic SAML | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315988"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Kurz: Integrace Azure Active Directory s Velpic SAML

V tomto kurzu se dozvíte, jak integrovat Velpic SAML s Azure Active Directory (Azure AD).
Integrace Velpic SAML s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Velpic SAML.
* Uživatelům se automaticky přihlášeni k Velpic SAML (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Velpic SAML, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Velpic SAML jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Velpic SAML **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-velpic-saml-from-the-gallery"></a>Přidání Velpic SAML z Galerie

Konfigurace integrace Velpic SAML do služby Azure AD, budete muset přidat Velpic SAML z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Velpic SAML z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Velpic SAML**vyberte **Velpic SAML** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Velpic SAML v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí Velpic SAML podle testovacího uživatele volá **Britta Simon**.
Vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Velpic SAML pro jednotné přihlašování pro práci, musí vytvořit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Velpic SAML, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Velpic SAML jednotného přihlašování](#configure-velpic-saml-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Velpic SAML](#create-velpic-saml-test-user)**  – Pokud chcete mít protějšek Britta Simon Velpic SAML, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí Velpic SAML, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Velpic SAML** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Domény Velpic SAML a adres URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<sub-domain>.velpicsaml.net`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Mějte prosím na paměti, že adresa URL přihlašování, poskytneme vám tým Velpic SAML a hodnotu identifikátoru budou k dispozici při konfiguraci jednotného přihlašování k modulu plug-in na straně Velpic SAML. Budete muset ze stránky aplikace Velpic SAML zkopírujte tuto hodnotu a vložte ho tady.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Velpic SAML** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-velpic-saml-single-sign-on"></a>Konfigurace Velpic SAML jednotného přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Velpic SAML.

2. Klikněte na **spravovat** kartu a přejít na **integrace** části, které je potřeba kliknout na **moduly plug-in** pro vytvoření nového modulu plug-in pro přihlášení.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_1.png)

3. Klikněte na **"Přidání modulu plug-in"** tlačítko.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_2.png)

4. Klikněte na **SAML** dlaždice na stránce Přidat zásuvný modul.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_3.png)

5. Zadejte název nové zásuvný modul SAML a klikněte na tlačítko **'Přidat'** tlačítko.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_4.png)

6. Zadejte podrobnosti o následujícím způsobem:

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. V **název** textového pole zadejte název modulu plug-in SAML.

    b. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** jste zkopírovali z **nakonfigurovat přihlašování** okna na webu Azure portal.

    c. V **zprostředkovatele metadat konfigurace** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal.

    d. Můžete také povolit SAML zřizování čas tím, že **"Automatické vytváření nových uživatelů"** zaškrtávací políčko. Pokud uživatel neexistuje mezi Velpic a tento příznak není povolen, přihlášení z Azure se nezdaří. Pokud je příznak povolené uživatele se automaticky zřídí do Velpic při přihlášení. 

    e. Kopírovat **jednotné přihlašování na adrese URL** z textu a vložte ji na webu Azure Portal.
    
    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Velpic SAML.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Velpic SAML**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Velpic SAML**.

    ![V seznamu aplikací na odkaz Velpic SAML](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-velpic-saml-test-user"></a>Vytvořit testovacího uživatele Velpic SAML

Tento krok se obvykle nevyžaduje jako aplikace podporuje pouze v době zřizování uživatelů. Pokud není povolena automatické zřizování uživatelů je vytvoření ruční uživatele provést, jak je popsáno níže.

Přihlaste se jako správce společnosti lokalitu Velpic SAML a proveďte následující kroky:
    
1. Klikněte na kartu spravovat a přejděte do části pro uživatele a potom klikněte na tlačítko Přidat uživatele.

    ![Přidat uživatele](./media/velpicsaml-tutorial/velpic_7.png)

2. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky.

    ![uživatel](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. V **křestní jméno** textového pole Název typu první Britta.

    b. V **příjmení** textového pole zadejte příjmení Simon.

    c. V **uživatelské jméno** textového pole zadejte uživatelské jméno Britta Simon.

    d. V **e-mailu** textového pole zadejte e-mailová adresa Brittasimon@contoso.com účtu.

    e. Je volitelný zbývající informace, vyplní jej v případě potřeby.
    
    f. Klikněte na **ULOŽIT**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Když kliknete na dlaždici Velpic SAML na přístupovém panelu, měli byste obdržet přihlašovací stránku Velpic SAML aplikace. Měli byste vidět **"Přihlaste se pomocí služby Azure AD"** tlačítko na přihlašovací stránce.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Klikněte na **"Přihlaste se pomocí služby Azure AD"** tlačítko pro přihlášení k Velpic pomocí svého účtu Azure AD.

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

