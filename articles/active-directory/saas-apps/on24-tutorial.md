---
title: 'Kurz: Integrace Azure Active Directory s ON24 virtuální prostředí SAML připojení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a připojení ON24 virtuální prostředí SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0827895d58b0b7633ee4543495014c62b5394312
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209485"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Kurz: Integrace Azure Active Directory s ON24 virtuální prostředí SAML připojení

V tomto kurzu se dozvíte, jak integrovat ON24 virtuální prostředí SAML připojení s Azure Active Directory (Azure AD).

Integrace ON24 virtuální prostředí SAML připojení s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ON24 virtuální prostředí SAML připojení.
- Uživatele, aby automaticky získat přihlášení k ON24 virtuální prostředí SAML připojení (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ON24 virtuální prostředí SAML připojení, potřebujete následující položky:

- Předplatné Azure AD
- Připojení ON24 virtuální prostředí SAML jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ON24 virtuální prostředí SAML připojení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Přidání ON24 virtuální prostředí SAML připojení z Galerie
Konfigurace integrace ON24 virtuální prostředí SAML připojení do služby Azure AD, budete muset přidat ON24 virtuální prostředí SAML připojení z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ON24 virtuální prostředí SAML připojení z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/on24-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **ON24 virtuální prostředí SAML připojení**vyberte **ON24 virtuální prostředí SAML připojení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ON24 virtuální prostředí SAML připojení na základě testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ON24 virtuální prostředí SAML připojení je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ON24 virtuální prostředí SAML připojení je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s připojením ON24 virtuální prostředí SAML, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele ON24 virtuální prostředí SAML připojení](#create-an-on24-virtual-environment-saml-connection-test-user)**  – Pokud chcete mít protějšek Britta Simon ON24 virtuální prostředí SAML připojení, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ON24 virtuální prostředí SAML připojení.

**Ke konfiguraci Azure AD jednotné přihlašování s připojením ON24 virtuální prostředí SAML, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **ON24 virtuální prostředí SAML připojení** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Klikněte na tlačítko **změnit jednotné přihlašování režim** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** intiated režimu:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. V **identifikátor** textové pole, zadejte adresu URL:

     **Adresa URL pro produkční prostředí**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **Adresa URL prostředí QA**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL:
    
     **Adresa URL pro produkční prostředí**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **Adresa URL prostředí QA**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klikněte na tlačítko **nastavit další adresy URL**. 

    d. V **stav přenosu** textové pole, zadejte adresu URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Pokud chcete nakonfigurovat aplikace v **SP** intiated režimu v **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** stáhnout příslušný certifikát, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Ke konfiguraci jednotného přihlašování na **ON24 virtuální prostředí SAML připojení** straně, budete muset odeslat certifikát nebo metadata, který jste si stáhli z webu Azure portal [ON24 virtuální prostředí SAML připojení tým podpory](https://www.on24.com/about-us/support/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/on24-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Vytvořit testovacího uživatele ON24 virtuální prostředí SAML připojení

V této části vytvořte uživatele Britta Simon ON24 virtuální prostředí SAML připojení. Práce s [tým podpory ON24 virtuální prostředí SAML připojení](https://www.on24.com/about-us/support/) přidat uživatele na platformě ON24 virtuální prostředí SAML připojení. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ON24 virtuální prostředí SAML připojení.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **ON24 virtuální prostředí SAML připojení**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ON24 virtuální prostředí SAML připojení na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ON24 virtuální prostředí SAML připojení.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

