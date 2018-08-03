---
title: 'Kurz: Integrace Azure Active Directory se SAP cloudem pro zákazníka | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP cloudem pro zákazníka.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 59bbcbf9aaef17394151e7db1471b63b87a46288
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445713"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Kurz: Integrace Azure Active Directory se SAP cloudem pro odběratele

V tomto kurzu se dozvíte, jak integrovat SAP cloudem pro zákazníky se službou Azure Active Directory (Azure AD).

Integrace SAP cloudem pro zákazníky s využitím Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAP cloudem pro odběratele
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SAP cloudem pro zákazníka (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se SAP cloudem pro zákazníka, potřebujete následující položky:

- S předplatným služby Azure AD
- SAP cloudem pro zákazníka jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP cloudem pro zákazníka z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Přidání SAP cloudem pro zákazníka z Galerie
Ke konfiguraci integrace SAP cloudem pro zákazníka do služby Azure AD, budete muset přidat SAP cloudem pro zákazníka v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAP cloudem pro zákazníka z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **SAP cloudem pro zákazníka**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

1. Na panelu výsledků vyberte **SAP cloudem pro zákazníka**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP cloudem pro zákazníky na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SAP cloudem pro zákazníka je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SAP cloudem pro zákazníka.

V SAP cloudem pro zákazníka, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se SAP cloudem pro zákazníka, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se SAP cloudem pro zákazníka testovacího uživatele](#creating-a-sap-cloud-for-customer-test-user)**  – Pokud chcete mít protějšek Britta Simon ve SAP Cloud for zákazníka, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašem Cloudovém SAP pro aplikace pro zákazníky.

**Ke konfiguraci Azure AD jednotného přihlašování se SAP cloudem pro zákazníky, proveďte následující kroky:**

1. Na webu Azure Portal na **SAP cloudem pro zákazníka** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

1. Na **SAP cloudem pro zákazníka domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server name>.crm.ondemand.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [SAP cloudem pro tým podpory zákazníků klienta](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) k získání těchto hodnot. 

1. Na **atributy uživatele** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. V **identifikátor uživatele** seznamu, vyberte **ExtractMailPrefix()** funkce.

    b. Z **e-mailu** vyberte atribut uživatele, které chcete použít pro implementaci.
    Například pokud chcete použít jako jedinečný identifikátor uživatele EmployeeID a hodnota atributu jsou uložené v ExtensionAttribute2, vyberte user.extensionattribute2.  

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

1. Na **SAP cloudem pro zákazníky Configuration** klikněte na tlačítko **konfigurace SAP cloudem pro zákazníka** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

1. Pokud chcete získat jednotné přihlašování nakonfigurovali, proveďte následující kroky:
   
    a. Přihlaste se k SAP cloudem pro zákaznického portálu služeb s oprávněním správce.
   
    b. Přejděte **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartu.
   
    c. Klikněte na tlačítko **nového zprostředkovatele Identity** a vyberte soubor XML metadat, který jste si stáhli z portálu Azure portal. Importováním metadata systému automaticky nahrává dodejka certifikát a certifikát pro šifrování.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory vyžaduje element adresa URL služby příjemce kontrolního výrazu v požadavku SAML, proto vyberte **zahrnují URL služby příjemce kontrolního výrazu** zaškrtávací políčko.
   
    e. Klikněte na tlačítko **aktivovat jednotné přihlašování**.
   
    f. Uložte provedené změny.
   
    g. Klikněte na tlačítko **systém** kartu.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. V **přihlášení Azure AD na adrese URL** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Určete, zda zaměstnance můžete ručně vybrat mezi variantami přihlášení pomocí ID uživatele a heslo nebo jednotného přihlašování tak, že vyberete **výběru zprostředkovatele Identity ruční**.
   
    j. V **adresu URL jednotného přihlašování** části, zadejte adresu URL, který se má použít vaši zaměstnanci k přihlášení k systému. 
    V **URL odesílat zaměstnance** seznamu, můžete si vybrat následující možnosti:
   
    **Adresa URL – jednotné přihlašování**
   
    Systém odešle jenom adresu URL normální systému zaměstnance. Zaměstnanec nelze přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo certifikátu.
   
    **ADRESA URL PRO JEDNOTNÉ PŘIHLAŠOVÁNÍ** 
   
    Systém odešle pouze adresu URL jednotného přihlašování pro zaměstnance. Zaměstnanec se může přihlásit pomocí jednotného přihlašování. Požadavek na ověření je přesměrován prostřednictvím zprostředkovatele identity.
   
    **Automatický výběr**
   
    Pokud jednotného přihlašování není aktivní, systém odešle URL normální systému zaměstnanci. Pokud se jednotné přihlašování je aktivní, systém zkontroluje, zda zaměstnance heslo. Pokud je k dispozici heslo, adresu URL jednotného přihlašování a adresy URL bez jednotného přihlašování se odesílají zaměstnanci. Ale pokud zaměstnanec nemá žádné heslo, pouze adresu URL jednotného přihlašování se odešle zaměstnanci.
   
    k. Uložte provedené změny.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Vytváří se SAP cloudem pro zákazníka testovacího uživatele

V této části vytvoříte uživateli Britta Simon v SAP cloudem pro zákazníka. Spojte se prosím s [SAP cloudem pro tým podpory](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) přidejte uživatele, v cloudu SAP pro platformu zákazníka. 

> [!NOTE]
> Ujistěte se prosím, že NameID hodnota by měla odpovídat s pole uživatelské jméno v SAP cloudem pro platformu zákazníka.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SAP cloudem pro zákazníka.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon SAP cloudem pro zákazníky, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **SAP cloudem pro zákazníka**.

    ![Konfigurace jednotného přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na SAP cloudem pro zákazníka dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k SAP cloudem pro aplikace pro zákazníky.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/sap-customer-cloud-tutorial/tutorial_general_203.png

