---
title: 'Kurz: Integrace Azure Active Directory se službou ArcGIS Online | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269030"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Integrace Azure Active Directory se službou ArcGIS Online

V tomto kurzu se dozvíte, jak integrovat ArcGIS Online se službou Azure Active Directory (Azure AD).

Integrace ArcGIS Online s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ArcGIS Online.
- Uživatele, aby automaticky získat přihlášení k ArcGIS Online (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ArcGIS Online, potřebujete následující položky:

- Předplatné Azure AD
- ArcGIS Online jednotné přihlašování povolené předplatným

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ArcGIS Online z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS Online z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, budete muset přidat ArcGIS Online z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ArcGIS Online z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Online**vyberte **ArcGIS Online** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ArcGIS Online na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ArcGIS Online je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ArcGIS online musí být vytvořeno.

ArcGIS online, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ArcGIS Online, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření ArcGIS Online testovacího uživatele](#create-an-arcgis-online-test-user)**  – Pokud chcete mít protějšek Britta Simon ArcGIS online, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ArcGIS Online.

**Ke konfiguraci Azure AD jednotné přihlašování s ArcGIS Online, postupujte následovně:**

1. V [webu Azure portal](https://portal.azure.com/)na **ArcGIS Online** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.maps.arcgis.com`.

    b. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory ArcGIS Online klienta](http://support.esri.com/en/) k získání těchto hodnot.

6. Na **podpisový certifikát SAML** části, klikněte na **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor xml ve vašem počítači.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. K automatizaci konfigurace v rámci **ArcGIS Online**, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/arcgis-tutorial/install_extension.png)

8. Po přidání rozšíření do prohlížeče, klikněte na **nastavení ArcGIS Online** nasměruje na ArcGIS Online aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do ArcGIS Online. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 13. 9.

9. Pokud chcete nastavit ArcGIS Online ručně, otevřete nové okno webového prohlížeče a přihlaste se jako správce ArcGIS společnosti lokalitu a proveďte následující kroky:

10. Klikněte na tlačítko **upravovat nastavení**.

    ![Upravit nastavení](./media/arcgis-tutorial/ic784742.png "upravit nastavení")

11. Klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/ic784743.png "zabezpečení")

12. V části **podnikové přihlašovací údaje**, klikněte na tlačítko **nastavit zprostředkovatele IDENTITY**.

    ![Podnikové přihlašovací údaje](./media/arcgis-tutorial/ic784744.png "podnikové přihlašovací údaje")

13. Na **nastavit zprostředkovatele Identity** konfigurační stránce, proveďte následující kroky:

    ![Nastavit zprostředkovatele Identity](./media/arcgis-tutorial/ic784745.png "nastavit zprostředkovatele Identity")

    a. V **název** textového pole zadejte název vaší organizace.

    b. Pro **Metadata pro zprostředkovatele Identity organizace bude zadán pomocí**vyberte **soubor**.

    c. Nahrát soubor stažený metadat, klikněte na tlačítko **zvolte soubor**.

    d. Klikněte na tlačítko **zprostředkovatele IDENTITY sada**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-an-arcgis-online-test-user"></a>Vytvoření ArcGIS Online testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do ArcGIS Online, musí být poskytnuty do ArcGIS Online.  
V případě ArcGIS Online zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **ArcGIS** tenanta.

2. Klikněte na tlačítko **pozvání členů**.
   
    ![Pozvání členů](./media/arcgis-tutorial/ic784747.png "zvát členy")

3. Vyberte **automaticky přidat členy bez odeslání e-mailu**a potom klikněte na tlačítko **Další**.
   
    ![Automaticky přidat členy](./media/arcgis-tutorial/ic784748.png "automaticky přidat členy")

4. Na **členy** dialogového okna stránky, proveďte následující kroky:
   
     ![Přidat a zkontrolujte](./media/arcgis-tutorial/ic784749.png "přidat a revize")
    
     a. Zadejte **e-mailu**, **křestní jméno**, a **příjmení** platného účtu AAD, které chcete zřídit.
  
     b. Klikněte na tlačítko **přidat a kontrola**.
5. Zkontrolujte data, která jste zadali a potom klikněte na tlačítko **přidat členy**.
   
    ![Přidat člena](./media/arcgis-tutorial/ic784750.png "přidat člena")
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ArcGIS Online.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ArcGIS Online na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci ArcGIS Online.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



