---
title: 'Kurz: Integrace Azure Active Directory s FiscalNote | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42dd7b23416e723d9c51cc065e61cfd7a1b3aaa9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198826"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Kurz: Integrace Azure Active Directory s FiscalNote

V tomto kurzu se dozvíte, jak integrovat FiscalNote s Azure Active Directory (Azure AD).

FiscalNote integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FiscalNote.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FiscalNote (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FiscalNote, potřebujete následující položky:

- Předplatné Azure AD
- FiscalNote jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FiscalNote z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-fiscalnote-from-the-gallery"></a>Přidání FiscalNote z Galerie
Konfigurace integrace FiscalNote do služby Azure AD, budete muset přidat FiscalNote z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat FiscalNote z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **FiscalNote**vyberte **FiscalNote** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![FiscalNote v seznamu výsledků](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí FiscalNote podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v FiscalNote je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v FiscalNote potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FiscalNote, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele FiscalNote](#create-a-fiscalnote-test-user)**  – Pokud chcete mít protějšek Britta Simon FiscalNote, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci FiscalNote.

**Ke konfiguraci Azure AD jednotné přihlašování s FiscalNote, proveďte následující kroky:**

1. Na webu Azure Portal na **FiscalNote** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

1. Na **FiscalNote domény a adresy URL** části, proveďte následující kroky:

    ![FiscalNote domény a adresy URL jednotného přihlašování – informace](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory FiscalNote klienta](mailto:support@fiscalnote.com) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

1. Aplikace FiscalNote očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci.

    ![Konfigurace jednotného přihlašování](./media/fiscalnote-tutorial/tutorial_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
           
    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | jméno | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | e-mail| user.mail|
    
    a. Odeberte existující atributy a přidejte nové atributy. Klikněte na **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Obor názvů, ponechte prázdné.
    
    e. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/fiscalnote-tutorial/tutorial_general_400.png)

1. Na **FiscalNote konfigurace** klikněte na tlačítko **nakonfigurovat FiscalNote** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **FiscalNote** straně, je nutné odeslat na stažený **Certificate(Raw), adresa URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [FiscalNote tým podpory](mailto:support@fiscalnote.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/fiscalnote-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/fiscalnote-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/fiscalnote-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-fiscalnote-test-user"></a>Vytvoření zkušebního uživatele FiscalNote

Cílem této části je vytvořte uživatele Britta Simon v FiscalNote. FiscalNote podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k FiscalNote, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory FiscalNote](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k FiscalNote použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit FiscalNote Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **FiscalNote**.

    ![Odkaz FiscalNote v seznamu aplikací](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici FiscalNote na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci FiscalNote.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

