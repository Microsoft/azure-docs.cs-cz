---
title: 'Kurz: Integrace Azure Active Directory s Phraseanet | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49a311b74fbc48f414f530e962af39d17a346353
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175536"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Kurz: Integrace Azure Active Directory s Phraseanet

V tomto kurzu se dozvíte, jak integrovat Phraseanet s Azure Active Directory (Azure AD).

Phraseanet integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Phraseanet.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Phraseanet (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Phraseanet, potřebujete následující položky:

- Předplatné Azure AD
- Phraseanet jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Phraseanet z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-phraseanet-from-the-gallery"></a>Přidání Phraseanet z Galerie
Konfigurace integrace Phraseanet do služby Azure AD, budete muset přidat Phraseanet z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Phraseanet z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Phraseanet**vyberte **Phraseanet** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Phraseanet podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Phraseanet je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Phraseanet potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Phraseanet, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Phraseanet](#create-a-phraseanet-test-user)**  – Pokud chcete mít protějšek Britta Simon Phraseanet, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Phraseanet.

**Ke konfiguraci Azure AD jednotné přihlašování s Phraseanet, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Phraseanet** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako:  `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Phraseanet](mailto:support@alchemy.fr) má být získána hodnota.
 
5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  a uložte ho do počítače.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Ke konfiguraci jednotného přihlašování na **Phraseanet** straně, je nutné odeslat na stažený **kód XML metadat federace** k [tým podpory Phraseanet](mailto:support@alchemy.fr). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-phraseanet-test-user"></a>Vytvoření zkušebního uživatele Phraseanet

V této části vytvoříte uživatele v Phraseanet jako Britta Simon. Práce s [tým podpory Phraseanet](mailto:support@alchemy.fr) přidat uživatele na platformě Phraseanet. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Phraseanet použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Phraseanet na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Phraseanet.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


