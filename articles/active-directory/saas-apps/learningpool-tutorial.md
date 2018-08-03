---
title: 'Kurz: Integrace Azure Active Directory se službou Learningpool Act | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Learningpool Act.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 6f3fab8398c8f32d4fa89f11c60fec57db516fcb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439432"
---
# <a name="tutorial-azure-active-directory-integration-with-learningpool-act"></a>Kurz: Integrace Azure Active Directory se službou Learningpool Act

V tomto kurzu se dozvíte, jak integrovat Learningpool Act s Azure Active Directory (Azure AD).

Learningpool Act integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Learningpool Act
- Uživatele, aby automaticky získat přihlášení k Learningpool Act (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Learningpool Act, potřebujete následující položky:

- S předplatným služby Azure AD
- Learningpool Act jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Learningpool Act z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-learningpool-act-from-the-gallery"></a>Přidání Learningpool Act z Galerie
Pokud chcete nakonfigurovat integraci sady Application Compatibility Toolkit Learningpool do služby Azure AD, budete muset přidat Learningpool Act z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Learningpool Act z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Learningpool Act**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/tutorial_Learningpoolact_search.png)

1. Na panelu výsledků vyberte **Learningpool Act**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/tutorial_Learningpoolact_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s Learningpool Act podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Learningpool Act je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Learningpool Act potřeba navázat.

V Learningpool Act přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Learningpool akce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Learningpool Act](#creating-a-learningpool-act-test-user)**  – Pokud chcete mít protějšek Britta Simon Learningpool úkon, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Learningpool Act.

**Ke konfiguraci Azure AD jednotné přihlašování s Learningpool Act, proveďte následující kroky:**

1. Na webu Azure Portal na **Learningpool Act** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_samlbase.png)

1. Na **Learningpool Act domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<subdomain>.Learningpool.com/shibboleth` |
    | `https://<subdomain>.preview.Learningpool.com/shibboleth` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Learningpool Act klienta](https://www.Learningpool.com/support) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_certificate.png) 

1. Learningpool Act aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **"Atrribute"** kartu aplikace. Následující snímek obrazovky ukazuje příklad pro tuto. 

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_attribute.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | název urn: oid:1.2.840.113556.1.4.221 | user.userprincipalname |
    | název urn: oid:2.5.4.42 | user.givenname |
    | urn:oid:0.9.2342.19200300.100.1.3 | User.Mail |    
    | název urn: oid:2.5.4.4 | user.surname |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Learningpool Act** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Learningpool Act](https://www.Learningpool.com/support). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learningpool-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-learningpool-act-test-user"></a>Vytvoření zkušebního uživatele Learningpool Act

Povolit uživatele Azure AD se přihlaste k Learningpool Act, musí být poskytnuty do Learningpool Act.

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Learningpool Act.  
Uživatelé musí být vytvořené vaší [tým podpory Learningpool Act](https://www.Learningpool.com/support).

>[!NOTE]
>Můžete použít jakékoli jiné Learningpool Act uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Learningpool Act uživatelským účtům, zřídit AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Learningpool Act.

![Přiřadit uživatele][200] 

**Přiřadit Learningpool Act Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Learningpool Act**.

    ![Konfigurace jednotného přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici Learningpool Act na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Learningpool Act.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/Learningpool-tutorial/tutorial_general_01.png
[2]: ./media/Learningpool-tutorial/tutorial_general_02.png
[3]: ./media/Learningpool-tutorial/tutorial_general_03.png
[4]: ./media/Learningpool-tutorial/tutorial_general_04.png

[100]: ./media/Learningpool-tutorial/tutorial_general_100.png

[200]: ./media/Learningpool-tutorial/tutorial_general_200.png
[201]: ./media/Learningpool-tutorial/tutorial_general_201.png
[202]: ./media/Learningpool-tutorial/tutorial_general_202.png
[203]: ./media/Learningpool-tutorial/tutorial_general_203.png

