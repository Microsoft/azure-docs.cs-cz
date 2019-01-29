---
title: 'Kurz: Integrace Azure Active Directory s RFPIO | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 0b216d8a8a2c6e1ab7c7b71eedfca9cbd6dbd5cf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197343"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Kurz: Integrace Azure Active Directory s RFPIO

V tomto kurzu se dozvíte, jak integrovat RFPIO s Azure Active Directory (Azure AD).

RFPIO integraci se službou Azure AD poskytuje následující výhody:

- Můžete určovat, kdo ve službě Azure AD, který má přístup k RFPIO.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k RFPIO (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RFPIO, potřebujete následující položky:

- Předplatné služby Azure AD.
- RFPIO jednotné přihlašování v povolené předplatné.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Pokud to není nutné používat vaše produkční prostředí.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RFPIO z galerie.
1. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-rfpio-from-the-gallery"></a>Přidání RFPIO z Galerie
Konfigurace integrace RFPIO do služby Azure AD, budete muset přidat RFPIO z Galerie na váš seznam spravovaných aplikací SaaS.

### <a name="to-add-rfpio-from-the-gallery"></a>Chcete-li přidat RFPIO z Galerie

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním podokně, vyberte **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko nahoře dialogové okno.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **RFPIO**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. Na panelu výsledků vyberte **RFPIO**a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí RFPIO podle testovacího uživatele nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co je vztah mezi uživatelem protějšky v RFPIO a uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v RFPIO potřeba navázat.

V RFPIO, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RFPIO, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**– aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**– k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele RFPIO](#creating-a-rfpio-test-user)**  – obsahovat protějšek Britta Simon RFPIO, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assigning-the-azure-ad-test-user)**– povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#testing-single-sign-on)**  – Chcete-li ověřit, pokud konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci RFPIO.

**Ke konfiguraci Azure AD jednotné přihlašování s RFPIO, proveďte následující kroky:**

1. Na webu Azure Portal na **RFPIO** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. Na **RFPIO domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://www.rfpio.com`

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Zkontrolujte **zobrazit pokročilé nastavení URL**.

    c. V **stav přenosu** textového pole zadejte hodnotu řetězce. Kontakt [tým podpory RFPIO](https://www.rfpio.com/contact/) tuto výhodu získáte. 

1. Zkontrolujte **zobrazit pokročilé nastavení URL**. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu: 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://www.app.rfpio.com`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče, přihlaste se k **RFPIO** webu jako správce.

1. Klikněte na rozevírací seznam dolním levém rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klikněte na **funkce a integrace**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app4.png)

1. V **Konfigurace jednotného přihlašování SAML** klikněte na tlačítko **upravit**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app3.png)

1. V této části provedete následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app5.png)
    
    a. Zkopírujte obsah **stáhnout soubor XML s metadaty** a vložte ho do **konfigurace identity** pole.

    > [!NOTE]
    >Zkopírujte obsah stáhnout **soubor XML s metadaty** použití **Poznámkový blok ++** nebo správné **editoru XML**. 

    b. Klikněte na tlačítko **ověření**.

    c. Po kliknutí na tlačítko **ověření**, Převrátit **SAML(Enabled)** zapnete.

    d. Klikněte na **Submit** (Odeslat).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rfpio-test-user"></a>Vytvoření zkušebního uživatele RFPIO

Přihlaste se k RFPIO Azure AD uživatelům umožnit, musí být poskytnuty do RFPIO.  
V případě RFPIO zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti RFPIO jako správce.

1. Klikněte na rozevírací seznam dolním levém rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klikněte na tlačítko **členové týmu**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app6.png)

1. Klikněte na **přidat členy**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app7.png)

1. V **přidat nové členy** oddílu. Proveďte následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app8.png)

    a. Zadejte **e-mailová adresa** v **zadejte jednu e-mailu na řádek** pole.

    b. Vyberte prosím znovu **Role** podle vašich požadavků.

    c. Klikněte na tlačítko **přidat členy**.
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k RFPIO použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit RFPIO Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **RFPIO**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici RFPIO na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci RFPIO.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

