---
title: 'Kurz: Integrace Azure Active Directory s CorpTax | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CorpTax.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: f372d0a1a9d16801103380559332bff282c7e096
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818213"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Kurz: Integrace Azure Active Directory s CorpTax

V tomto kurzu se dozvíte, jak integrovat CorpTax s Azure Active Directory (Azure AD).
CorpTax integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k CorpTax.
* Můžete povolit uživatelům být automaticky přihlášeni k CorpTax (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s CorpTax, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* CorpTax jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje CorpTax **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-corptax-from-the-gallery"></a>Přidání CorpTax z Galerie

Konfigurace integrace CorpTax do služby Azure AD, budete muset přidat CorpTax z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat CorpTax z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select_azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise_applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add_new_app.png)

4. Do vyhledávacího pole zadejte **CorpTax**vyberte **CorpTax** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![CorpTax v seznamu výsledků](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí CorpTax podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v CorpTax.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s CorpTax, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace CorpTax Single Sign-On](#configure-corptax-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytvořit testovacího uživatele CorpTax](#create-corptax-test-user)**  – Pokud chcete mít protějšek Britta Simon CorpTax, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s CorpTax, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **CorpTax** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select_saml_option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit_urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![CorpTax domény a adresy URL jednotného přihlašování – informace](common/sp_intiated.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://asp.corptax.com`

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Konfigurace CorpTax jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **CorpTax** straně, je nutné odeslat na stažený **kód XML metadat federace** k [tým podpory CorpTax](mailto:bgraves@corptax.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new_user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user_properties.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k CorpTax použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **CorpTax**.

    ![Okno aplikace organizace](common/enterprise_applications.png)

2. V seznamu aplikace zadejte a vyberte **CorpTax**.

    ![Odkaz CorpTax v seznamu aplikací](common/all_applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users_groups_blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add_assign_user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-corptax-test-user"></a>Vytvoření CorpTax testovacího uživatele

V této části vytvoříte uživatele v CorpTax jako Britta Simon. Práce s [tým podpory CorpTax](mailto:bgraves@corptax.com) přidat uživatele na platformě CorpTax. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.
Když kliknete na dlaždici CorpTax na přístupovém panelu, měli byste být přesměrovaní na pod CorpTax stránky - 

![image](media/corptax-tutorial/corptaxlogin.png)

V **prostředí** textového pole zadejte příslušné prostředí, které by měl být automaticky přihlášeni k CorpTax, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
