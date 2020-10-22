---
title: 'Kurz: Azure Active Directory integrace s Mitel Connect | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mitel Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: 044ee51fb7ff675af0fb97224313c388c4cbeac1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371337"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Kurz: Integrace Azure Active Directory s platformou Mitel MiCloud Connect nebo dodavatelů CloudLink

V tomto kurzu se naučíte, jak pomocí aplikace Mitel Connect integrovat Azure Active Directory (Azure AD) s Mitel MiCloud Connect nebo dodavatelů CloudLink Platform. Aplikace Mitel Connect je dostupná v galerii Azure. Integrace Azure AD s platformou MiCloud Connect nebo dodavatelů CloudLink Platform vám poskytne následující výhody:

* Přístup uživatelů k aplikacím MiCloud Connect a aplikacím dodavatelů CloudLink ve službě Azure AD můžete řídit pomocí svých podnikových přihlašovacích údajů.
* Můžete povolit, aby se uživatelé na vašem účtu automaticky přihlásili k MiCloud Connect nebo dodavatelů CloudLink (jednotné přihlašování) pomocí svých účtů Azure AD.

Podrobnosti o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete s integrací služby Azure AD s platformou Mitel MiCloud Connect nebo dodavatelů CloudLink.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s MiCloud Connect budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Mitel MiCloud Connect nebo účet Mitel dodavatelů CloudLink v závislosti na aplikaci, kterou chcete nakonfigurovat.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování (SSO) Azure AD.

* Mitel Connect podporuje jednotné přihlašování iniciované v **SP**
* Jakmile nakonfigurujete Mitel Connect, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Přidání Mitel Connect z Galerie

Pokud chcete nakonfigurovat integraci Mitel připojení ke službě Azure AD, musíte přidat Mitel připojit z Galerie do seznamu spravovaných aplikací pro SaaS v Azure Portal.

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Vyberte **Nová aplikace**.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Mitel připojit** , vyberte **Mitel připojit** z panelu výsledků a pak vyberte **Přidat**.

     ![Mitel Connect v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s platformou MiCloud Connect nebo dodavatelů CloudLink na základě testovacího uživatele s názvem **_Britta Simon_**. Aby jednotné přihlašování fungovalo, musí být mezi uživatelem na portálu Azure AD a odpovídajícím uživatelem na platformě Mitel vytvořen odkaz. V následujících částech najdete informace o konfiguraci a testování jednotného přihlašování služby Azure AD s platformou MiCloud Connect nebo dodavatelů CloudLink.
* Konfigurace a testování jednotného přihlašování Azure AD pomocí MiCloud Connect
* Konfigurace a testování jednotného přihlašování Azure AD s platformou dodavatelů CloudLink

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Konfigurace a testování jednotného přihlašování Azure AD pomocí MiCloud Connect

Konfigurace a testování jednotného přihlašování Azure AD pomocí MiCloud Connect:

1. **[Nakonfigurujte MiCloud Connect pro jednotné přihlašování pomocí Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – umožníte uživatelům používat tuto funkci a nakonfigurovat nastavení jednotného přihlašování na straně aplikace.
2. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
3. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
4. **[Vytvořte testovacího uživatele Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** – abyste měli protějšek Britta Simon na svém účtu MiCloud Connect, který je propojený s reprezentací uživatele v Azure AD.
5. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurace MiCloud Connect pro jednotné přihlašování pomocí Azure AD

V této části povolíte jednotné přihlašování Azure AD pro MiCloud Connect v Azure Portal a nakonfigurujete účet MiCloud Connect tak, aby povoloval jednotné přihlašování pomocí Azure AD.

Pokud chcete nakonfigurovat MiCloud Connect s SSO pro Azure AD, je nejjednodušší otevřít Azure Portal a portál účtu Mitel vedle sebe. Na portálu účtů Mitel budete muset zkopírovat některé informace z Azure Portal na portál účtu Mitel a některé z portálu účtů na Azure Portal.


1. Chcete-li otevřít stránku konfigurace v [Azure Portal](https://portal.azure.com/):

    1. Na stránce integrace aplikace **Mitel Connect** vyberte **jednotné přihlašování**.

       ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

    1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML**.
    
       ![Režim výběru jednotného přihlašování](common/select-saml-option.png)
    
       Zobrazí se přihlašovací stránka založená na SAML.

2. Chcete-li otevřít dialogové okno Konfigurace na portálu účtů Mitel:

    1. V nabídce **systém pro telefon** vyberte **funkce doplňku**.

    1. Napravo od **jednotného přihlašování**vyberte **aktivovat** nebo **Nastavení**.
    
    Zobrazí se dialogové okno připojit nastavení jednoho Sign-On.
    
3. Zaškrtněte políčko **Povolit jednotné přihlašování** .
    
    ![Snímek obrazovky, který zobrazuje stránku nastavení Mitel Connect Single Sign-On, se zaškrtnutým políčkem Povolit jednu Sign-On](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. V Azure Portal vyberte v části **základní konfigurační oddíl SAML** ikonu **Upravit** .
   
    ![Snímek obrazovky s vybranou ikonou pro úpravu zobrazí stránku nastavit jednu Sign-On se stránkou SAML.](common/edit-urls.png)

    Zobrazí se základní dialogové okno Konfigurace SAML.

5.  Zkopírujte adresu URL z pole **identifikátor Mitel (ID entity)** na portálu účtů Mitel a vložte ho do pole **identifikátor (ID entity)** v Azure Portal.

6. Zkopírujte adresu URL z pole **Adresa URL odpovědi (adresa URL služby potvrzení)** na portálu účtu Mitel a vložte ji do pole **Adresa URL odpovědi (adresa URL služby potvrzení)** v Azure Portal.

   ![Snímek obrazovky ukazuje základní konfiguraci SAML v Azure Portal a v části nastavení zprostředkovatele identity na portálu účtů Mitel s řádky, které označují vztah mezi nimi.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Do textového pole **přihlašovací adresa URL** zadejte jednu z těchto adres URL:

    1. **https://portal.shoretelsky.com** – použití portálu účtu Mitel jako výchozí aplikace Mitel
    1. **https://teamwork.shoretel.com** – použití týmové práce jako výchozí aplikace Mitel

    > [!NOTE]
    > Výchozí aplikace Mitel je aplikace, ke které se přistupuje, když uživatel vybere dlaždici Mitel připojit na přístupovém panelu. To je také aplikace, ke které se přistupoval při instalaci testu z Azure AD.

8. V Azure Portal základní dialogové okno **Konfigurace SAML** vyberte **Uložit** .

9. V části **podpisový certifikát SAML** na **přihlašovací stránce založené na SAML** v Azure Portal vyberte **Stáhnout** certifikát **(Base64)** a stáhněte **podpisový certifikát** a uložte ho do počítače.

    ![Snímek obrazovky se zobrazí v podokně podpisový certifikát SAML, kde si můžete stáhnout certifikát.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Otevřete soubor podpisového certifikátu v textovém editoru, zkopírujte všechna data v souboru a vložte data do pole **podpisový certifikát** na portálu účtů Mitel. 

      ![Snímek obrazovky se zobrazí pole podpisový certifikát.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. V části **Nastavení Mitel Connect** na přihlašovací stránce **založené na SAML** Azure Portal:

     1. Zkopírujte adresu URL z pole **Adresa URL pro přihlášení** a vložte ji do pole **Adresa URL pro přihlášení** na portálu účtu Mitel.

     1. Zkopírujte adresu URL z pole **identifikátoru Azure AD** a vložte ji do pole **ID entity** na portálu účtu Mitel.
         
         ![Snímek obrazovky ukazuje relaci mezi přihlašovací stránkou založenou na SAML Azure Portal a portálem účtu Mitel.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. V dialogovém okně **připojit nastavení jednoho Sign-On** na portálu účtu Mitel vyberte **Save (Uložit** ).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně Vlastnosti uživatele proveďte následující kroky:

    ![Uživatelský dialog](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte brittasimon@ \<yourcompanydomain\> . \<extension\> .  Například, BrittaSimon@contoso.com.

    1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Mitel připojení.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Mitel připojit**.

    ![Odkaz Mitel Connect v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** položku **Britta Simon** a pak zvolte **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte příslušnou roli pro uživatele ze seznamu v dialogovém okně **Vybrat roli** a pak zvolte **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Vytvoření testovacího uživatele Mitel MiCloud Connect

V této části vytvoříte uživatele s názvem Britta Simon na účtu MiCloud Connect. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

Podrobnosti o přidávání uživatelů na portálu účtů Mitel najdete v článku věnovaném [Přidání uživatele](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) ve znalostní bázi Mitel.

Na účtu MiCloud Connect vytvořte uživatele s následujícími podrobnostmi:

* **Název:** Britta Simon
* **Pracovní e-mailová adresa:**`brittasimon@<yourcompanydomain>.<extension>`   
  (Příklad: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) )
* **Uživatelské jméno:**`brittasimon@<yourcompanydomain>.<extension>`  
  (Příklad: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) ; uživatelské jméno uživatele je obvykle stejné jako pracovní e-mailová adresa uživatele.)

> [!NOTE]
> Uživatelské jméno MiCloud připojení uživatele musí být stejné jako e-mailová adresa uživatele v Azure.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Mitel Connect, měli byste se automaticky přesměrovat, abyste se přihlásili do aplikace MiCloud Connect, kterou jste nakonfigurovali jako výchozí v poli **Adresa URL pro přihlášení** . Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Konfigurace a testování jednotného přihlašování Azure AD s platformou dodavatelů CloudLink

Tato část popisuje, jak povolit službu Azure AD SSO pro dodavatelů CloudLink Platform v Azure Portal a jak nakonfigurovat účet platformy dodavatelů CloudLink pro povolení jednotného přihlašování pomocí Azure AD.

Pokud chcete nakonfigurovat platformu dodavatelů CloudLink s jednotným přihlašováním pro Azure AD, doporučujeme, abyste otevřeli Azure Portal a portál účty dodavatelů CloudLink vedle sebe, jak budete muset zkopírovat některé informace z Azure Portal na portál účtů dodavatelů CloudLink a naopak.

1. Chcete-li otevřít stránku konfigurace v [Azure Portal](https://portal.azure.com/):

    1. Na stránce integrace aplikace **Mitel Connect** vyberte **jednotné přihlašování**.

       ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

    1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost **SAML**.

       ![Režim výběru jednotného přihlašování](common/select-saml-option.png)
    
       Otevře se **přihlašovací stránka založená na SAML** a zobrazí se **základní konfigurační oddíl SAML** .

       ![Snímek obrazovky ukazuje přihlašovací stránku založenou na SAML se základní konfigurací SAML.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Přístup ke konfiguračnímu panelu **jednotné přihlašování Azure AD** na portálu účtů dodavatelů CloudLink:

    1. Přejít na stránku s **informacemi o účtu** účtu zákazníka, se kterým chcete povolit integraci.

    1. V části **integrace** vyberte **+ Přidat nový**. Automaticky otevíraná obrazovka se zobrazí panel **integrace** .

    1. Vyberte kartu **třetí strana** . Zobrazí se seznam podporovaných aplikací třetích stran. Vyberte tlačítko **Přidat** přidružené k **jednotnému přihlašování Azure AD**a vyberte **Hotovo**.

       ![Snímek obrazovky zobrazující stránku integrace, kde můžete přidat Azure A D jednotného přihlašování.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       Pro účet zákazníka je povolené **jednotné přihlašování Azure AD** a přidá se do části **integrace** na stránce **informace o účtu** .   

   1. Vyberte **Dokončit instalaci**.
    
      ![Snímek obrazovky ukazuje možnost kompletního nastavení pro Azure A D jednotného přihlašování.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Otevře se panel konfigurace **jednotného přihlašování služby Azure AD** .
      
       ![Snímek obrazovky ukazuje konfiguraci pro jednu Sign-On Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel doporučuje, aby nebylo zaškrtnuto políčko **Povolit Mitel přihlašovací údaje (volitelné)** v oddílu **volitelné přihlašovací údaje Mitel** . Toto políčko zaškrtněte pouze v případě, že chcete, aby se uživatel přihlásil k aplikaci dodavatelů CloudLink pomocí přihlašovacích údajů Mitel společně s možností jednotného přihlašování.

3. V Azure Portal na **přihlašovací stránce založené na SAML** vyberte v části **základní konfigurace SAML** ikonu **Upravit** . Otevře se **základní panel konfigurace SAML** .

    ![Snímek obrazovky se základní podokno konfigurace SAML s vybranou ikonou pro úpravy](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Zkopírujte adresu URL z pole **identifikátor Mitel (ID entity)** na portálu účtů dodavatelů CloudLink a vložte ho do pole **identifikátor (ID entity)** v Azure Portal.

 5. Zkopírujte adresu URL z pole **Adresa URL odpovědi (adresa URL služby potvrzení)** na portálu účty dodavatelů CloudLink a vložte ji do pole **Adresa URL odpovědi (adresa URL služby potvrzení)** v Azure Portal.  
    
    ![Snímek obrazovky ukazuje vztah mezi stránkami na portálu účtů dodavatelů CloudLink a Azure Portal.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Do textového pole **přihlašovací adresa URL** zadejte adresu URL `https://accounts.mitel.io` pro použití portálu účtů dodavatelů CloudLink jako výchozí aplikace Mitel.
     
     ![Snímek obrazovky se zobrazí v textovém poli přihlášení U R L.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Výchozí aplikace Mitel je aplikace, která se otevře, když uživatel vybere dlaždici Mitel připojit na přístupovém panelu. To je také aplikace, ke které se používá, když uživatel nakonfiguruje nastavení testu z Azure AD.

7. V dialogovém okně **základní konfigurace SAML** vyberte **Save (Uložit** ).

8. V části **podpisový certifikát SAML** na **přihlašovací stránce založené na SAML** v Azure Portal vyberte **Stáhnout** certifikát **(Base64)** a stáhněte **podpisový certifikát**. Uložte certifikát do počítače.
  
    ![Snímek obrazovky se zobrazí v části podpisový certifikát SAML, kde si můžete stáhnout certifikát base64.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. V textovém editoru otevřete soubor podpisového certifikátu, zkopírujte do něj všechna data a pak data vložte do pole **podpisový certifikát** na portálu účtů dodavatelů CloudLink.  

    > [!NOTE]
    > Pokud máte více než jeden certifikát, doporučujeme, abyste je vložili po druhém. 
       
    ![Snímek obrazovky ukazuje krok 2 v postupu, ve kterém vyplníte hodnoty z Integrace Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. V části **Nastavení Mitel Connect** na **přihlašovací stránce založené na SAML** Azure Portal:

     1. Zkopírujte adresu URL z pole **Adresa URL pro přihlášení** a vložte ji do pole **Adresa URL pro přihlášení** na portálu účtů dodavatelů CloudLink.

     1. Zkopírujte adresu URL z pole **identifikátoru Azure AD** a vložte ji do pole **identifikátor IDP (ID entity)** na portálu účtů dodavatelů CloudLink.
     
        ![Snímek obrazovky zobrazující zdroj hodnot popsaných tady v Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Na panelu **jednotné přihlašování Azure AD** na portálu účtů dodavatelů CloudLink vyberte **Save (Uložit** ).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V dialogovém okně Vlastnosti uživatele proveďte následující kroky:

    ![Uživatelský dialog](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte brittasimon@ \<yourcompanydomain\> . \<extension\> .  Například, BrittaSimon@contoso.com.

    1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu, která se zobrazí v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Mitel připojení.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Mitel připojit**.

    ![Odkaz Mitel Connect v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** položku **Britta Simon** a pak zvolte **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte příslušnou roli pro uživatele ze seznamu v dialogovém okně **Vybrat roli** a pak zvolte **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-cloudlink-test-user"></a>Vytvořit testovacího uživatele v dodavatelů CloudLink

Tato část popisuje, jak vytvořit testovacího uživatele s názvem **_Britta Simon_** na platformě dodavatelů CloudLink. Aby bylo možné používat jednotné přihlašování, musí být uživatelé vytvořeni a aktivováni.

Podrobnosti o přidávání uživatelů na portálu účtů dodavatelů CloudLink najdete v tématu **_Správa uživatelů_** v dokumentaci k [účtům dodavatelů CloudLink](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Na portálu účtů dodavatelů CloudLink vytvořte uživatele s následujícími podrobnostmi:

* Název: Britta Simon
* Křestní jméno: Britta
* Příjmení: Simon
* E-mail: BrittaSimon@contoso.com

> [!NOTE]
> E-mailová adresa uživatele dodavatelů CloudLink musí být shodná s **hlavním názvem uživatele** v Azure Portal.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když vyberete dlaždici Mitel Connect na přístupovém panelu, budete automaticky přesměrováni na přihlášení k aplikaci dodavatelů CloudLink, kterou jste nakonfigurovali jako výchozí v poli **Adresa URL pro přihlášení** . Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)