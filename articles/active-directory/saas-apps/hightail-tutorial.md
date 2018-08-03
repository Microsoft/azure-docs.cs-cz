---
title: 'Kurz: Integrace Azure Active Directory se službou Hightail | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 1151044d5c1002c808ae1214086aff5fad84a55e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431330"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Kurz: Integrace Azure Active Directory se službou Hightail

V tomto kurzu se dozvíte, jak integrovat Hightail s Azure Active Directory (Azure AD).

Hightail integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Hightail
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Hightail (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Hightail, potřebujete následující položky:

- S předplatným služby Azure AD
- Hightail jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Hightail z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hightail-from-the-gallery"></a>Přidání Hightail z Galerie
Konfigurace integrace Hightail do služby Azure AD, budete muset přidat Hightail z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Hightail z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Hightail**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/tutorial_hightail_search.png)

1. Na panelu výsledků vyberte **Hightail**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Hightail podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Hightail je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Hightail potřeba navázat.

V Hightail, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Hightail, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Hightail](#creating-a-hightail-test-user)**  – Pokud chcete mít protějšek Britta Simon Hightail, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Hightail.

**Ke konfiguraci Azure AD jednotné přihlašování s Hightail, proveďte následující kroky:**

1. Na webu Azure Portal na **Hightail** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. Na **Hightail domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL jako: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není skutečné hodnoty. Skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu jste se aktualizuje hodnotu adresy URL odpovědi.

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://www.hightail.com/loginSSO`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. Hightail aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **"Atribut"** kartu aplikace. Následující snímek obrazovky ukazuje příklad pro tuto. 

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | User.Mail |    
    | Identity uživatele | User.Mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.

    e. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_general_400.png)

1. Na **Hightail konfigurace** klikněte na tlačítko **nakonfigurovat Hightail** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Před konfigurací jednotné přihlašování v aplikaci Hightail, prosím seznamu povolených vaší e-mailové domény Hightail tým tak, aby všichni uživatelé, kteří používají tuto doménu můžete použít funkci jednotného přihlašování.

1. V jiném okně prohlížeče, otevřete **Hightail** portál pro správu.

1. Klikněte na **ikonu uživatele** v pravém horním rohu stránky. 

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/configure1.png)

1. Klikněte na tlačítko **zobrazení konzoly pro správu** kartu.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/configure2.png)

1. V nabídce v horní části klikněte **SAML** kartě a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/configure3.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanými z webu Azure portal.

    b. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát SAML** textového pole.

    c. Klikněte na tlačítko **kopírování** zkopírovat adresu URL příjemce SAML pro vaši instanci a vložte ji **adresy URL odpovědi** textového pole v **Hightail domény a adresy URL** části na webu Azure portal.

    d. Klikněte na tlačítko **ukládání konfigurace**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hightail-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-hightail-test-user"></a>Vytvoření zkušebního uživatele Hightail

Cílem této části je vytvořte uživatele Britta Simon v Hightail. 

Neexistuje žádná položka akce pro vás v této části. Hightail podporuje zřizování uživatelů just-in-time podle vlastních deklarací identity. Pokud jste nakonfigurovali vlastní deklarace identity, jak je uvedeno v části **[konfigurace služby Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** výše, uživatel se automaticky vytvoří v aplikaci ještě neexistuje. 

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Hightail použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Hightail Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Hightail**.

    ![Konfigurace jednotného přihlašování](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Hightail na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Hightail.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

