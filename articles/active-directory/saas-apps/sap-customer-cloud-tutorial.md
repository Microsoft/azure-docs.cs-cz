---
title: 'Kurz: Azure Active Directory integrace s cloudem SAP zákazníka. | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP cloudu pro zákazníka.
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
ms.openlocfilehash: 661bb15f93c505ad069e826b1506f6e69c5339e0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223344"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Kurz: Azure Active Directory integrace s cloudem SAP zákazníka.

V tomto kurzu zjistěte, jak integrovat SAP cloudu pro zákazníka s Azure Active Directory (Azure AD).

Integrace SAP cloudu pro zákazníka s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do cloudu SAP zákazníka.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SAP cloudu pro zákazníka (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudem SAP pro zákazníka, potřebujete následující položky:

- Předplatné služby Azure AD
- Cloud SAP pro zákazníka jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP cloudu pro zákazníka z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Přidání SAP cloudu pro zákazníka z Galerie
Při konfiguraci integrace SAP cloudu pro zákazníka do služby Azure AD, potřebujete přidat SAP cloudu pro zákazníka z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SAP cloudu pro zákazníka z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SAP cloudu pro zákazníka**.

    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. Na panelu výsledků vyberte **SAP cloudu pro zákazníka**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování s cloudem SAP pro zákazníka na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v cloudu SAP pro zákazníka je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v cloudu SAP pro zákazníka.

V cloudu SAP pro zákazníka, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s cloudem SAP pro zákazníka, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření cloudu SAP pro zákazníka testovacího uživatele](#creating-a-sap-cloud-for-customer-test-user)**  – Pokud chcete mít protějšek Britta Simon v cloudu SAP zákazníkovi, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování ve vašem cloudu SAP pro aplikace pro zákazníky.

**Ke konfiguraci Azure AD jednotné přihlašování s cloudem SAP pro zákazníka, proveďte následující kroky:**

1. Na portálu Azure na **SAP cloudu pro zákazníka** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. Na **SAP cloudu zákazníka domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server name>.crm.ondemand.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [SAP Cloud pro tým podpory zákazníků klienta](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) k získání těchto hodnot. 

4. Na **uživatelské atributy** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. V **uživatelský identifikátor** seznamu, vyberte **ExtractMailPrefix()** funkce.

    b. Z **e-mailu** vyberte atribut uživatele, kterou chcete použít týkající se vaší implementace.
    Například pokud chcete použít EmployeeID jako jedinečný identifikátor uživatele a hodnota atributu jsou uloženy v ExtensionAttribute2, pak vyberte user.extensionattribute2.  

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

7. Na **SAP cloudu pro konfiguraci zákazníka** klikněte na tlačítko **konfigurace cloudu SAP pro zákazníka** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Potřebujete nakonfigurovat jednotné přihlašování, proveďte následující kroky:
   
    a. Přihlášení do cloudu SAP pro zákaznický portál s právy správce.
   
    b. Přejděte na **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartě.
   
    c. Klikněte na tlačítko **nového poskytovatele Identity** a vyberte soubor XML metadat, který jste si stáhli z portálu Azure. Importováním metadata systém automaticky nahrává dodejka certifikát a certifikát pro šifrování.
   
    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory vyžaduje element adresa URL služby Assertion příjemce v žádosti o SAML, vyberte **zahrnují Assertion příjemce adresa URL služby** zaškrtávací políčko.
   
    e. Klikněte na tlačítko **aktivovat jednotné přihlašování**.
   
    f. Uložte provedené změny.
   
    g. Klikněte **Moje systému** kartě.
   
    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. V **Azure AD adresa URL přihlašování** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Zadejte, zda zaměstnanec ručně vybrat mezi přihlásit pomocí ID uživatele a heslo nebo jednotného přihlašování k výběrem **výběr zprostředkovatele Identity ruční**.
   
    j. V **jednotného přihlašování k adrese URL** části, zadejte adresu URL, která má být zaměstnancům používána k přihlášení k systému. 
    V **URL posílá zaměstnanec** seznamu, můžete zvolit z následujících možností:
   
    **Adresa URL jednotného přihlašování**
   
    Systém odešle jenom adresu URL normální systému zaměstnanci. Zaměstnanec nemůže přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo toho certifikátu.
   
    **ADRESA URL JEDNOTNÉHO PŘIHLAŠOVÁNÍ** 
   
    Systém odešle zaměstnanec pouze adresy URL pro jednotné přihlašování. Zaměstnanec může přihlásit pomocí jednotného přihlašování. Prostřednictvím rozšíření IdP přesměruje požadavek na ověření.
   
    **Automatický výběr**
   
    Pokud jednotného přihlašování není aktivní, odešle systému zaměstnanec adresu URL normální systému. Pokud je aktivní jednotné přihlašování, systém kontroluje, zda zaměstnanec má heslo. Pokud heslo je k dispozici, jednotného přihlašování k URL a adresy URL bez jednotného přihlašování k odešlou do jednotlivých zaměstnanců. Ale pokud zaměstnanec, nemá žádné heslo, pouze adresy URL pro jednotné přihlašování odešle zaměstnanci.
   
    k. Uložte provedené změny.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Vytvoření cloudu SAP pro zákazníka testovacího uživatele

V této části vytvoříte uživatele volat Britta Simon v cloudu SAP pro zákazníka. Spojte se s [SAP Cloud pro tým podpory zákazníků](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) přidejte uživatele v cloudu SAP pro platformu zákazníka. 

> [!NOTE]
> Zkontrolujte, zda hodnota NameID by měl odpovídat pole uživatelské jméno v cloudu SAP pro platformu zákazníka.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu do cloudu SAP pro zákazníka.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon SAP cloudu pro zákazníka, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP cloudu pro zákazníka**.

    ![Konfigurovat jednotné přihlašování](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko SAP cloudu pro dlaždice zákazníka na přístupovém panelu, jste měli získat automaticky přihlášení k SAP cloudu pro aplikace pro zákazníky.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

