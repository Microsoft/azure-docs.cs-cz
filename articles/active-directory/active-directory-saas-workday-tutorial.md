---
title: 'Kurz: Azure Active Directory integraci s Workday | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a během pracovního dne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 5f3c3bbb23b9d4439188286342d80eb1cc1d0833
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Kurz: Azure Active Directory integraci s Workday

V tomto kurzu zjistěte, jak integrovat Workday s Azure Active Directory (Azure AD).

Integrace Workday s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Workday.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Workday (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li konfigurovat Azure AD integraci s Workday, potřebujete následující položky:

- Předplatné služby Azure AD
- Workday jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Workday z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workday-from-the-gallery"></a>Přidání Workday z Galerie
Konfigurace integrace Workday do Azure AD, potřebujete přidat Workday z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Workday z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Workday**, vyberte **Workday** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![WORKDAY v seznamu výsledků](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workday podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Workday je pro uživatele ve službě Azure AD. Jinými slovy musí odkaz vztah mezi uživatele Azure AD a související uživatelské ve Workday zřídit.

V pracovního dne, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Workday, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Workday](#create-a-workday-test-user)**  – Pokud chcete mít protějšek Britta Simon ve Workday propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci během pracovního dne.

**Ke konfiguraci Azure AD jednotné přihlašování s Workday, proveďte následující kroky:**

1. Na portálu Azure na **Workday** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. Na **Workday domény a adresy URL** část, proveďte následující kroky:

    ![WORKDAY domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `http://www.workday.com`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok:

    ![WORKDAY domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování a adresa URL odpovědi. Vaše adresa URL odpovědi musí mít například subdoména: www, wd2, wd3, wd3 impl, wd5, wd5 impl). Pomocí něco podobného jako "*http://www.myworkday.com*" funguje, ale "*http://myworkday.com*" neexistuje. Obraťte se na [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) k získání těchto hodnot.  

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. Na **Workday konfigurace** klikněte na tlačítko **konfigurace Workday** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![WORKDAY konfigurace](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Workday jako správce.

9. V **vyhledávacího pole** hledání s názvem **upravit nastavení klienta – zabezpečení** nahoře levé straně domovské stránky.
   
    ![Upravit zabezpečení klienta](./media/active-directory-saas-workday-tutorial/IC782925.png "upravit klienta zabezpečení")

10. V **adresy URL pro přesměrování** část, proveďte následující kroky:
   
    ![Adresy URL pro přesměrování](./media/active-directory-saas-workday-tutorial/IC7829581.png "adresy URL pro přesměrování")
   
    a. Klikněte na tlačítko **přidejte řádek**.
   
    b. V **adresy URL přesměrování při přihlášení** textové pole a **adresu URL pro přesměrování mobilní** textovému poli, typ **přihlašovací adresa URL** jste zadali na **Workday domény a adresy URL** části portálu Azure.
   
    c. Na portálu Azure na **konfigurovat přihlášení** okno, kopie **Sign-Out URL**a vložte ji do **adresy URL přesměrování odhlašovací** textové pole.

    d. V **používá pro prostředí** textovému poli, vyberte název prostředí.  

    >[!NOTE]
    > Hodnota atributu prostředí je vázaný na hodnotu adresy URL klienta:  
    >-Pokud název domény adresy URL klienta Workday začíná impl například:  *https://impl.workday.com/ \<klienta\>/login-saml2.htmld*), **prostředí** musí být nastaven pro implementaci.  
    >– Pokud je název domény začíná něco jiného, budete muset kontaktovat [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) získat shody **prostředí** hodnotu.

11. V **SAML instalace** část, proveďte následující kroky:
   
    ![Instalační program SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "instalace SAML")
   
    a.  Vyberte **povolit ověřování SAML**.
   
    b.  Klikněte na tlačítko **přidejte řádek**.

12. V **zprostředkovatelů Identity SAML** část, proveďte následující kroky:
   
    ![Zprostředkovatelé Identity SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "zprostředkovatelů Identity SAML")
   
    a. V **název zprostředkovatele Identity** textovému poli, zadejte název zprostředkovatele (například: *SPInitiatedSSO*).
   
    b. Na portálu Azure na **konfigurovat přihlášení** okno, kopie **SAML Entity ID** hodnotu a vložte ji do **vystavitele** textové pole.

    ![Zprostředkovatelé Identity SAML](./media/active-directory-saas-workday-tutorial/IC7829272.png "zprostředkovatelů Identity SAML")
   
    c. Na portálu Azure na **konfigurovat přihlášení** okno, kopie **Sign-Out URL** hodnotu a vložte ji do **adresa URL odpovědi odhlášení** textové pole.

    d. Na portálu Azure na **konfigurovat přihlášení** okno, kopie **SAML jeden přihlašování adresa URL služby** hodnotu a vložte ji do **IdP adresa URL služby jednotného přihlašování k** textové pole.

    e. V **používá pro prostředí** textovému poli, vyberte název prostředí.

    f. Klikněte na tlačítko **certifikátu veřejného klíče zprostředkovatele Identity**a potom klikněte na **vytvořit**. 

    ![Vytvoření](./media/active-directory-saas-workday-tutorial/IC782928.png "vytvořit")

    g. Klikněte na tlačítko **vytvořit x509 veřejný klíč**. 

    ![Vytvoření](./media/active-directory-saas-workday-tutorial/IC782929.png "vytvořit")

13. V **veřejný klíč zobrazení x509** část, proveďte následující kroky: 
   
    ![Veřejný klíč zobrazení x509](./media/active-directory-saas-workday-tutorial/IC782930.png "zobrazení x509 veřejný klíč") 
   
    a. V **název** textovému poli, zadejte název pro svůj certifikát (například: *PPE\_SP*).
   
    b. V **platný z** textovému poli, zadejte platný z hodnota atributu vašeho certifikátu.
   
    c.  V **platný k** textovému poli, zadejte platnou hodnotu atributu vašeho certifikátu.
   
    > [!NOTE]
    > Můžete získat od data a platném datum, od stažený certifikát poklepáním platný.  Kalendářní data jsou uvedeny v seznamu **podrobnosti** kartě.
    > 
    >
   
    d.  Otevření kódovaného certifikátu kódování base-64 v programu Poznámkový blok a zkopírujte obsah ho.
   
    e.  V **certifikát** textovému poli, vložit obsah schránky.
   
    f.  Klikněte na **OK**.

14. Proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování k](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Konfigurace jednotného přihlašování")
   
    a.  V **ID zprostředkovatele služby** textovému poli, typ **http://www.workday.com**.
   
    b. Vyberte **není Deflate žádosti o ověření spouštěná SP**.
   
    c. Jako **metoda žádosti o podpis ověřování**, vyberte **SHA256**. 
   
    ![Metoda ověřování žádost o podpis](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "metodu ověřování žádost o podpis") 
   
    d. Klikněte na **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-workday-test-user"></a>Vytvoření zkušebního uživatele Workday

V této části vytvoříte uživatele volal Britta Simon v Workday. Práce s [tým podpory klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) přidat uživatele do Workday platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k Workday.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Workday, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Workday**.

    ![V seznamu aplikací na Workday odkaz](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Workday na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Workday.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png
