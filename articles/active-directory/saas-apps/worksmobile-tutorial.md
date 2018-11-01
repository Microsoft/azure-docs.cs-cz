---
title: 'Kurz: Integrace Azure Active Directory s řádku FUNGUJE | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a řádku PRACUJE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 0437bd46537d8b7024618706aba12660abca5512
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414688"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Kurz: Integrace Azure Active Directory s řádku FUNGUJE

V tomto kurzu se dozvíte, jak integrovat řádku FUNGUJE s Azure Active Directory (Azure AD).

Integrace řádku FUNGUJE s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k řádku FUNGUJE.
- Uživatele, aby automaticky získat přihlášení k řádku FUNGUJE (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s řádku FUNGUJE, potřebujete následující položky:

- Předplatné Azure AD
- ŘÁDKU FUNGUJE jednotné přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání řádku FUNGUJE z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-line-works-from-the-gallery"></a>Přidání řádku FUNGUJE z Galerie
Konfigurace integrace FUNGUJE řádku do služby Azure AD, budete muset přidat řádek FUNGUJE z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat řádek FUNGUJE z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/worksmobile-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/worksmobile-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/worksmobile-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **řádku FUNGUJE**vyberte **řádku FUNGUJE** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí řádku FUNGUJE na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v řádku FUNGUJE je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v řádku FUNGUJE.

Ke konfiguraci a testování Azure AD jednotné přihlašování FUNGUJE řádku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele řádku FUNGUJE](#create-a-line-works-test-user)**  – Pokud chcete mít protějšek Britta Simon v řádku FUNGUJE, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci FUNGUJE řádku.

**Ke konfiguraci Azure AD jednotné přihlašování s řádku FUNGUJE, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **řádku FUNGUJE** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/worksmobile-tutorial/b1_b2_select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/worksmobile-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/worksmobile-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.worksmobile.com/d/login/<domain>/?userId=<ID@domain>`

    b. V **identifikátor** textové pole, zadejte adresu URL: `worksmobile.com`

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** a uložte ho do počítače.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_certficate.png)

6. Na **nastavení FUNGUJE řádku** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![image](./media/worksmobile-tutorial/d1_samlsonfigure.png) 

7. Ke konfiguraci jednotného přihlašování na **řádku FUNGUJE** straně, přečtěte si prosím [jednotné přihlašování FUNGUJE řádku dokumenty](https://developers.worksmobile.com/jp/document/1001080101) a proveďte konfiguraci nastavení řádku FUNGUJE.

> [!NOTE]
> Je potřeba převést na .pem stažený soubor certifikátu z .cert


### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/worksmobile-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/worksmobile-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/worksmobile-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-line-works-test-user"></a>Vytvoření zkušebního uživatele řádku FUNGUJE

V této části vytvoříte uživatele v řádku FUNGUJE jako Britta Simon. Přístup [stránky pro správu řádku FUNGUJE](https://admin.worksmobile.com) a přidejte uživatele na platformě FUNGUJE řádku.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k řádku FUNGUJE.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/worksmobile-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **řádku FUNGUJE**.

    ![image](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/worksmobile-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/worksmobile-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici řádku FUNGUJE na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci řádku FUNGUJE.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


