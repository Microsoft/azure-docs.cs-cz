---
title: 'Kurz: Integrace Azure Active Directory s SAP Business ByDesign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9846fe8078403d064f226c121383fc191397d321
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201869"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory s SAP Business ByDesign

V tomto kurzu se dozvíte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD).

SAP Business ByDesign integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAP Business ByDesign.
- Uživatele, aby automaticky získat přihlášení k SAP Business ByDesign (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP Business ByDesign, potřebujete následující položky:

- Předplatné Azure AD
- SAP Business ByDesign jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP Business ByDesign z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z Galerie
Konfigurace integrace SAP Business ByDesign do služby Azure AD, budete muset přidat SAP Business ByDesign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP Business ByDesign z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **SAP Business ByDesign**vyberte **SAP Business ByDesign** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SAP Business ByDesign v seznamu výsledků](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SAP Business ByDesign podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SAP Business ByDesign je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SAP Business ByDesign musí být vytvořeno.

V SAP Business ByDesign přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP Business ByDesign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP Business ByDesign, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SAP Business ByDesign.

**Ke konfiguraci Azure AD jednotného přihlašování se SAP Business ByDesign, proveďte následující kroky:**

1. Na webu Azure Portal na **SAP Business ByDesign** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

1. Na **SAP Business ByDesign domény a adresy URL** části, proveďte následující kroky:

    ![SAP Business ByDesign domény a adresy URL jednotného přihlašování – informace](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<servername>.sapbydesign.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) k získání těchto hodnot.

1. Na **atributy uživatele** části, proveďte následující kroky:

    ![SAP Business ByDesign atribut oddílu](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. V **identifikátor uživatele** seznamu, vyberte **ExtractMailPrefix()** funkce.
    
    b. Z **e-mailu** vyberte atribut uživatele, které chcete použít pro implementaci. Například pokud chcete použít jako jedinečný identifikátor uživatele EmployeeID a hodnota atributu jsou uložené v ExtensionAttribute2, vyberte user.extensionattribute2.     

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

1. Na **konfigurace SAP Business ByDesign** klikněte na tlačítko **konfigurace SAP Business ByDesign** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace SAP Business ByDesign](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, postupujte následovně:
   
    a. Přihlaste se k portálu SAP Business ByDesign s oprávněním správce.
   
    b. Přejděte do **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartu.
   
    c. Klikněte na tlačítko **nového zprostředkovatele Identity** a vyberte soubor XML metadat, který jste si stáhli z portálu Azure portal. Importováním metadata systému automaticky nahrává dodejka certifikát a certifikát pro šifrování.
   
    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Zahrnout **adresa URL služby Assertion příjemce** do požadavku SAML, vyberte **zahrnují URL služby příjemce kontrolního výrazu**.
   
    e. Klikněte na tlačítko **aktivovat jednotné přihlašování**.
   
    f. Uložte provedené změny.
   
    g. Klikněte na tlačítko **systém** kartu.
   
    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **přihlášení Azure AD na adrese URL** textového pole.
   
    ![Konfigurace jednotného přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Určete, zda zaměstnance můžete ručně vybrat mezi variantami přihlášení pomocí ID uživatele a heslo nebo jednotného přihlašování tak, že vyberete **výběru zprostředkovatele Identity ruční**.
   
    j. V **adresu URL jednotného přihlašování** části, zadejte adresu URL, který se má použít pro zaměstnance na přihlášení k systému. 
    V adresu URL odeslané na zaměstnance rozevírací seznam můžete vybrat mezi následujícími možnostmi:
   
    **Adresa URL – jednotné přihlašování**
   
    Systém odešle jenom adresu URL normální systému zaměstnance. Zaměstnanec nelze přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo certifikátu.
   
    **ADRESA URL PRO JEDNOTNÉ PŘIHLAŠOVÁNÍ** 
   
    Systém odešle pouze adresu URL jednotného přihlašování pro zaměstnance. Zaměstnanec se může přihlásit pomocí jednotného přihlašování. Požadavek na ověření je přesměrován prostřednictvím zprostředkovatele identity.
   
    **Automatický výběr**
   
    Pokud jednotného přihlašování není aktivní, systém odešle URL normální systému zaměstnanci. Pokud se jednotné přihlašování je aktivní, systém zkontroluje, zda zaměstnance heslo. Pokud je k dispozici heslo, adresu URL jednotného přihlašování a adresy URL bez jednotného přihlašování se odesílají zaměstnanci. Ale pokud zaměstnanec nemá žádné heslo, pouze adresu URL jednotného přihlašování se odešle zaměstnanci.
   
    k. Uložte provedené změny.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Vytvořit testovacího uživatele SAP Business ByDesign

V této části vytvoříte uživatele v SAP Business ByDesign jako Britta Simon. Spojte se prosím s [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) přidat uživatele na platformě SAP Business ByDesign. 

> [!NOTE]
> Ujistěte se prosím, že NameID hodnota by měla odpovídat s polem uživatelské jméno na platformě SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP Business ByDesign.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k SAP Business ByDesign Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SAP Business ByDesign**.

    ![V seznamu aplikací na odkaz SAP Business ByDesign](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SAP Business ByDesign na přístupovém panelu, vám by měl získat automaticky přihlášení k SAP Business ByDesign aplikace.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png

