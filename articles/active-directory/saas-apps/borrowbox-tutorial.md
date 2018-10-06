---
title: 'Kurz: Integrace Azure Active Directory se službou BorrowBox | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: jeedes
ms.openlocfilehash: 16252f690bfd34e596b2b8db26ab3a2ede0dfdee
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832215"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Kurz: Integrace Azure Active Directory se službou BorrowBox

V tomto kurzu se dozvíte, jak integrovat BorrowBox s Azure Active Directory (Azure AD).

BorrowBox integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BorrowBox.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BorrowBox (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BorrowBox, potřebujete následující položky:

- Předplatné Azure AD
- BorrowBox jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BorrowBox z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-borrowbox-from-the-gallery"></a>Přidání BorrowBox z Galerie
Konfigurace integrace BorrowBox do služby Azure AD, budete muset přidat BorrowBox z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BorrowBox z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/borrowbox-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/borrowbox-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/borrowbox-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **BorrowBox**vyberte **BorrowBox** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí BorrowBox podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v BorrowBox je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BorrowBox potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BorrowBox, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele BorrowBox](#create-a-borrowbox-test-user)**  – Pokud chcete mít protějšek Britta Simon BorrowBox, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci BorrowBox.

**Ke konfiguraci Azure AD jednotné přihlašování s BorrowBox, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **BorrowBox** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/borrowbox-tutorial/B1_B2_Select_SSO.png)

2. Klikněte na tlačítko **změnit jednotné přihlašování režim** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/borrowbox-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/borrowbox-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/borrowbox-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. Klikněte na tlačítko **nastavit další adresy URL**.

    b. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory BorrowBox klienta](mailto:borrowbox@bolinda.com) má být získána hodnota. 

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** stáhnout příslušný certifikát, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

7. Ke konfiguraci jednotného přihlašování na **BorrowBox** straně, budete muset odeslat certifikát nebo metadata, který jste si stáhli z webu Azure portal [tým podpory BorrowBox](mailto:borrowbox@bolinda.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/borrowbox-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/borrowbox-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/borrowbox-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-borrowbox-test-user"></a>Vytvoření zkušebního uživatele BorrowBox

Cílem této části je vytvořte uživatele Britta Simon v BorrowBox. BorrowBox podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k BorrowBox, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory BorrowBox](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k BorrowBox použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/borrowbox-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/borrowbox-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/borrowbox-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici BorrowBox na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci BorrowBox.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
