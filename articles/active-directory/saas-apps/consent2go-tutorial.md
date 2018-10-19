---
title: 'Kurz: Integrace Azure Active Directory se službou Consent2Go | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Consent2Go.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea93bc02-58ca-4468-84ff-359888fc6183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 89fc0b69a3a7ca3f795b4ae0e79b11a4bcd9c9fb
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430927"
---
# <a name="tutorial-azure-active-directory-integration-with-consent2go"></a>Kurz: Integrace Azure Active Directory se službou Consent2Go

V tomto kurzu se dozvíte, jak integrovat Consent2Go s Azure Active Directory (Azure AD).

Consent2Go integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Consent2Go.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Consent2Go (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Consent2Go, potřebujete následující položky:

- Předplatné Azure AD
- Consent2Go jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Consent2Go z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-consent2go-from-the-gallery"></a>Přidání Consent2Go z Galerie
Konfigurace integrace Consent2Go do služby Azure AD, budete muset přidat Consent2Go z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Consent2Go z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/consent2go-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/consent2go-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/consent2go-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Consent2Go**vyberte **Consent2Go** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/consent2go-tutorial/tutorial_consent2go_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Consent2Go podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Consent2Go je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Consent2Go potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Consent2Go, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Consent2Go](#create-a-consent2go-test-user)**  – Pokud chcete mít protějšek Britta Simon Consent2Go, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Consent2Go.

**Ke konfiguraci Azure AD jednotné přihlašování s Consent2Go, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Consent2Go** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/consent2go-tutorial/b1_b2_select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/consent2go-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/consent2go-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako:  `   https://www.mcbschools.com/Login`

    ![image](./media/consent2go-tutorial/tutorial_consent2go_url.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na ikonu kopírování zkopírujte **adresa Url federačních metadat aplikace** a uložte ho do počítače.

    ![image](./media/consent2go-tutorial/tutorial_consent2go_certificate.png) 

6. Ke konfiguraci jednotného přihlašování na **Consent2Go** straně, je nutné odeslat zkopírovaný **adresa Url federačních metadat aplikace** k [tým podpory Consent2Go](mailto:support@consent2go.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/consent2go-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/consent2go-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/consent2go-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-consent2go-test-user"></a>Vytvoření zkušebního uživatele Consent2Go

V této části vytvoříte uživatele v Consent2Go jako Britta Simon. Práce s [tým podpory Consent2Go](mailto:support@consent2go.com) přidat uživatele na platformě Consent2Go. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Consent2Go použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/consent2go-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Consent2Go**.

    ![image](./media/consent2go-tutorial/tutorial_consent2go_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/consent2go-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/consent2go-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Consent2Go na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Consent2Go.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


