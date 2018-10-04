---
title: 'Kurz: Integrace Azure Active Directory se službou Expensify | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248036"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Kurz: Integrace Azure Active Directory se službou Expensify

V tomto kurzu se dozvíte, jak integrovat Expensify s Azure Active Directory (Azure AD).

Expensify integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Expensify.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Expensify (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Expensify, potřebujete následující položky:

- Předplatné Azure AD
- Expensify jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Expensify z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-expensify-from-the-gallery"></a>Přidání Expensify z Galerie

Konfigurace integrace Expensify do služby Azure AD, budete muset přidat Expensify z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Expensify z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **Expensify**vyberte **Expensify** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Expensify podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Expensify je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Expensify potřeba navázat.

V Expensify, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Expensify, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Expensify](#create-an-expensify-test-user)**  – Pokud chcete mít protějšek Britta Simon Expensify, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Expensify.

**Ke konfiguraci Azure AD jednotné přihlašování s Expensify, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **Expensify** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://www.expensify.com/authentication/saml/login`

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Nahradit <companyname> část identifikátoru adresy URL s doménou vaší společnosti. Podívejte se příklad `https://contoso.expensify.com` výše. V Expensify, jde o název domény uvedené **Nastavení > domény řízení**.

    ![Expensify informace o doméně](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** stáhnout příslušný certifikát, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Pokud chcete povolit jednotné přihlašování v Expensify, je nejprve potřeba povolit **ovládací prvek domény** v aplikaci. Ovládací prvek domény můžete povolit v aplikaci pomocí kroků uvedených v tomto [tady](http://help.expensify.com/domain-control). Potřebujete další podporu, pracovat s [tým podpory Expensify klienta](mailto:help@expensify.com). Jakmile budete mít povolený nástroj řízení domény, postupujte podle těchto kroků:
   
    ![Konfigurace jednotného přihlašování](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Přihlaste se k aplikaci Expensify.
    
    b. Na levém panelu klikněte na tlačítko **nastavení** a přejděte do **SAML**.
    
    c. Přepnout **SAML přihlášení** možnost jako **povoleno**.
    
    d. Otevřete stažený federační Metadata ze služby Azure AD v programu Poznámkový blok, zkopírujte obsah a vložte jej do **metadat zprostředkovatele Identity** textového pole.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-expensify-test-user"></a>Vytvořit testovacího uživatele Expensify

V této části vytvoříte uživatele v Expensify jako Britta Simon. Práce s [tým podpory Expensify klienta](mailto:help@expensify.com) přidat uživatele na platformě Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Expensify použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Expensify na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Expensify.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)




