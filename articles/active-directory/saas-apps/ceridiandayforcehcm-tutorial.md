---
title: 'Kurz: Integrace Azure Active Directory se službou Ceridian Dayforce HCM | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6577955b275adfda3f0cfafe99a8f95efd16403c
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714576"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Kurz: Integrace Azure Active Directory se službou Ceridian Dayforce HCM

V tomto kurzu se dozvíte, jak integrovat Ceridian Dayforce HCM s Azure Active Directory (Azure AD).

Integrace Ceridian Dayforce HCM s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Ceridian Dayforce HCM.
- Uživatele, aby automaticky získat přihlášení k Ceridian Dayforce HCM (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Ceridian Dayforce HCM, potřebujete následující položky:

- Předplatné Azure AD
- Ceridian Dayforce HCM jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Ceridian Dayforce HCM z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Přidání Ceridian Dayforce HCM z Galerie
Konfigurace integrace Ceridian Dayforce HCM do služby Azure AD, budete muset přidat Ceridian Dayforce HCM z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Ceridian Dayforce HCM z galerie, postupujte následovně:**

1. V ** [webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Ceridian Dayforce HCM**vyberte **Ceridian Dayforce HCM** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Ceridian Dayforce HCM v seznamu výsledků](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí HCM Dayforce Ceridian podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Ceridian Dayforce HCM je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Ceridian Dayforce HCM potřeba navázat.

V Ceridian Dayforce HCM, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Ceridian Dayforce HCM, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on) ** – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) ** – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user) ** – Pokud chcete mít protějšek Britta Simon Ceridian Dayforce HCM, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) ** – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on) ** – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Ceridian Dayforce HCM.

**Ke konfiguraci Azure AD jednotné přihlašování s Ceridian Dayforce HCM, proveďte následující kroky:**

1. Na webu Azure Portal na **Ceridian Dayforce HCM** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

1. Na **Ceridian Dayforce HCM domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL používají vaši uživatelé k přihlašování do aplikace Ceridian Dayforce HCM.
    
    | Prostředí | zprostředkovatele identity |
    | :-- | :-- |
    | Pokud se jedná o výrobu | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Pro test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    
    | Prostředí | zprostředkovatele identity |
    | :-- | :-- |
    | Pokud se jedná o výrobu | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Pro test | `https://fs-test.dayforcehcm.com/sp` |
    
    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL Azure AD používá k odeslání odpovědi.
    
    | Prostředí | zprostředkovatele identity |
    | :-- | :-- |
    | Pokud se jedná o výrobu | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Pro test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Ceridian Dayforce HCM klienta](https://www.ceridian.com/support) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

1. Vaše aplikace Ceridian Dayforce HCM očekává, že kontrolní výrazy SAML v určitém formátu. Práce s [tým podpory Ceridian Dayforce HCM](https://www.ceridian.com/support) nejprve k identifikaci identifikátor správné uživatele. Společnost Microsoft doporučuje používat **"name"** atribut jako identifikátor uživatele. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.  

    ![Konfigurace jednotného přihlašování](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |    
    | jméno  | User.extensionattribute2 |    

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. V **hodnotu** vyberte atribut uživatele, které chcete použít pro implementaci.
    Například pokud chcete použít EmployeeID jako jedinečná uživatelská identifikátor a můžete mít uložené hodnotu atributu v ExtensionAttribute2, pak vyberte **user.extensionattribute2**.
    
    d. Klikněte na tlačítko **OK**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
1. Na **Ceridian Dayforce HCM konfigurace** klikněte na tlačítko **konfigurace HCM Dayforce Ceridian** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace HCM Ceridian Dayforce](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Ceridian Dayforce HCM** straně, je nutné odeslat na stažený **soubor XML s metadaty** a **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [tým podpory Ceridian Dayforce HCM](https://www.ceridian.com/support).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím ** Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ceridiandayforcehcm-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/ceridiandayforcehcm-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ceridiandayforcehcm-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Vytvoření zkušebního uživatele Ceridian Dayforce HCM

Cílem této části je vytvořte uživatele Britta Simon v Ceridian Dayforce HCM. Práce s [tým podpory Ceridian Dayforce HCM](https://www.ceridian.com/support) získat uživatelé přidaní v aplikaci Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Ceridian Dayforce HCM.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Ceridian Dayforce HCM, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Ceridian Dayforce HCM**.

    ![Konfigurace jednotného přihlašování](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Ceridian Dayforce HCM.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Ceridian Dayforce HCM, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Ceridian Dayforce HCM**.

    ![Odkaz Ceridian Dayforce HCM v seznamu aplikací](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  
Po kliknutí na dlaždici Ceridian Dayforce HCM na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Ceridian Dayforce HCM. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_203.png

