---
title: 'Kurz: Integrace Azure Active Directory se službou UserEcho | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ecdd37db662c6861e35f80bfbf4ac8ff7e0d08c9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443261"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Kurz: Integrace Azure Active Directory se službou UserEcho

V tomto kurzu se dozvíte, jak integrovat UserEcho s Azure Active Directory (Azure AD).

UserEcho integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k UserEcho
- Můžete povolit uživatelům, aby automaticky získat přihlášení k UserEcho (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UserEcho, potřebujete následující položky:

- S předplatným služby Azure AD
- UserEcho jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání UserEcho z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-userecho-from-the-gallery"></a>Přidání UserEcho z Galerie
Konfigurace integrace UserEcho do služby Azure AD, budete muset přidat UserEcho z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat UserEcho z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **UserEcho**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/tutorial_userecho_search.png)

1. Na panelu výsledků vyberte **UserEcho**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí UserEcho podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v UserEcho je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v UserEcho potřeba navázat.

V UserEcho, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserEcho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele UserEcho](#creating-a-userecho-test-user)**  – Pokud chcete mít protějšek Britta Simon UserEcho, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci UserEcho.

**Ke konfiguraci Azure AD jednotné přihlašování s UserEcho, proveďte následující kroky:**

1. Na webu Azure Portal na **UserEcho** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. Na **UserEcho domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.userecho.com/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory UserEcho klienta](https://feedback.userecho.com/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_general_400.png)

1. Na **UserEcho konfigurace** klikněte na tlačítko **nakonfigurovat UserEcho** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. V jiném okně prohlížeče Přihlaste se k webu společnosti UserEcho jako správce.

1. Na panelu nástrojů v horní části klikněte na své uživatelské jméno, rozbalte nabídku a pak klikněte na tlačítko **nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. Klikněte na tlačítko **integrace**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. Klikněte na tlačítko **webu**a potom klikněte na tlačítko **jednotné přihlašování (SAML2)**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. Na **jednotné přihlašování (SAML)** stránce, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **povoleno SAML**vyberte **Ano**.
    
    b. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **adresu URL jednotného přihlašování SAML** textového pole.
    
    c. Vložit **odhlašování URL**, který jste zkopírovali z portálu Azure portal do **URL vzdálené logoout** textového pole.
    
    d. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát X.509** textového pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/userecho-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-userecho-test-user"></a>Vytvoření zkušebního uživatele UserEcho

Cílem této části je vytvořte uživatele Britta Simon v UserEcho.

**Vytvořte uživatele v UserEcho jako Britta Simon, proveďte následující kroky:**

1. Přihlašování k webu společnosti UserEcho jako správce.

1. Na panelu nástrojů v horní části klikněte na své uživatelské jméno, rozbalte nabídku a pak klikněte na tlačítko **nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_06.png)

1. Klikněte na tlačítko **uživatelé**, rozbalte **uživatelé** oddílu.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_10.png)

1. Klikněte na tlačítko **uživatelé**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_11.png)

1. Klikněte na tlačítko **pozvat nového uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_12.png)

1. Na **pozvat nového uživatele** dialogového okna, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. V **název** textového pole zadejte jméno uživatele, jako Britta Simon.
    
    b.  V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.
    
    c. Klikněte na tlačítko **pozvat**.

Pozvánka se pošle Britta, která umožňuje ji chcete začít používat UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k UserEcho použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit UserEcho Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **UserEcho**.

    ![Konfigurace jednotného přihlašování](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici UserEcho na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci UserEcho.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

