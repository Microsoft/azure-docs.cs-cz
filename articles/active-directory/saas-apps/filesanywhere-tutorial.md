---
title: 'Kurz: Integrace Azure Active Directory s FilesAnywhere | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FilesAnywhere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54cbcb19e400f08e1e1d8e97afaa2e2e1cff133c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205830"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Kurz: Integrace Azure Active Directory s FilesAnywhere

V tomto kurzu se dozvíte, jak integrovat FilesAnywhere s Azure Active Directory (Azure AD).

FilesAnywhere integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FilesAnywhere
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FilesAnywhere (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FilesAnywhere, potřebujete následující položky:

- Předplatné Azure AD
- FilesAnywhere jednotného přihlašování povolená předplatného


> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.


Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FilesAnywhere z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-filesanywhere-from-the-gallery"></a>Přidání FilesAnywhere z Galerie
Konfigurace integrace FilesAnywhere do služby Azure AD, budete muset přidat FilesAnywhere z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat FilesAnywhere z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **FilesAnywhere**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. Na panelu výsledků vyberte **FilesAnywhere**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí FilesAnywhere podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v FilesAnywhere je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v FilesAnywhere potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v FilesAnywhere.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FilesAnywhere, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele FilesAnywhere](#creating-a-filesanywhere-test-user)**  – Pokud chcete mít protějšek Britta Simon FilesAnywhere, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci FilesAnywhere.

**Ke konfiguraci Azure AD jednotné přihlašování s FilesAnywhere, proveďte následující kroky:**

1. V portálu pro správu Azure na **FilesAnywhere** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. Na **FilesAnywhere domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**:

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Pamatujte, že hodnota **215** je **clientid** a je uvedené jenom jako příklad. Budete muset nahraďte ho hodnotou skutečné clientid.

1. Na **FilesAnywhere domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Klikněte na **zobrazit pokročilé nastavení URL** možnost

    b. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Kontakt [tým podpory FilesAnywhere](mailto:support@FilesAnywhere.com) k získání těchto hodnot. 

1. FilesAnywhere softwarová aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Když uživatelé přihlásí pomocí FilesAnywhere získávají hodnotu **clientid** atribut z [FilesAnywhere týmu](mailto:support@FilesAnywhere.com). Je nutné přidat atribut "Id klienta" s jedinečnou hodnotu poskytovanou infrastrukturou FilesAnywhere. Tyto atributy uvedené výše jsou povinné.
    > [!NOTE] 
    > Pamatujte, že hodnota **2331** z **clientid** je jenom pro příklad. Budete muset poskytnout skutečnou hodnotu.


1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | ID klienta | *"uniquevalue"* |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. Na **FilesAnywhere konfigurace** klikněte na tlačítko **nakonfigurovat FilesAnywhere** otevřete **nakonfigurovat přihlašování** okna.

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Získejte kompletní Konfigurace jednotného přihlašování pro vaši aplikaci na konci FilesAnywhere, obraťte se na [tým podpory FilesAnywhere](mailto:support@FilesAnywhere.com) a poskytování přesných stažené podpisový certifikát a jednotné přihlašování (SSO) adresy URL tokenu SAML.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-filesanywhere-test-user"></a>Vytvoření zkušebního uživatele FilesAnywhere

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. 


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k FilesAnywhere.

![Přiřadit uživatele][200] 

**Přiřadit FilesAnywhere Britta Simon, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **FilesAnywhere**.

    ![Konfigurace jednotného přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    


### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici FilesAnywhere na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci FilesAnywhere.


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
