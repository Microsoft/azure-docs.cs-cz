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
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 24a82bbaf47153791da2f21a0b68c2f81c0670e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446352"
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

- S předplatným služby Azure AD
- ArcGIS Online jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ArcGIS Online z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-arcgis-online-from-the-gallery"></a>Přidání ArcGIS Online z Galerie
Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, budete muset přidat ArcGIS Online z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ArcGIS Online z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **ArcGIS Online**vyberte **ArcGIS Online** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ArcGIS Online v seznamu výsledků](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ArcGIS Online na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ArcGIS Online je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ArcGIS online musí být vytvořeno.

ArcGIS online, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ArcGIS Online, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele ArcGIS Online](#create-a-arcgis-online-test-user)**  – Pokud chcete mít protějšek Britta Simon ArcGIS online, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ArcGIS Online.

**Ke konfiguraci Azure AD jednotné přihlašování s ArcGIS Online, postupujte následovně:**

1. Na webu Azure Portal na **ArcGIS Online** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

1. Na **ArcGIS Online domény a adresy URL** části, proveďte následující kroky:

    ![ArcGIS Online domény a adresy URL jednotného přihlašování – informace](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.maps.arcgis.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory ArcGIS Online klienta](http://support.esri.com/en/) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/arcgis-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče přihlaste jako správce webu ArcGIS společnosti.

1. Klikněte na tlačítko **upravovat nastavení**.

    ![Upravit nastavení](./media/arcgis-tutorial/ic784742.png "upravit nastavení")

1. Klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/ic784743.png "zabezpečení")

1. V části **podnikové přihlašovací údaje**, klikněte na tlačítko **nastavit zprostředkovatele IDENTITY**.

    ![Podnikové přihlašovací údaje](./media/arcgis-tutorial/ic784744.png "podnikové přihlašovací údaje")

1. Na **nastavit zprostředkovatele Identity** konfigurační stránce, proveďte následující kroky:
   
    ![Nastavit zprostředkovatele Identity](./media/arcgis-tutorial/ic784745.png "nastavit zprostředkovatele Identity")
   
    a. V **název** textového pole zadejte název vaší organizace.

    b. Pro **Metadata pro zprostředkovatele Identity organizace bude zadán pomocí**vyberte **soubor**.

    c. Nahrát soubor stažený metadat, klikněte na tlačítko **zvolte soubor**.

    d. Klikněte na tlačítko **zprostředkovatele IDENTITY sada**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/arcgis-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/arcgis-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/arcgis-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/arcgis-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-arcgis-online-test-user"></a>Vytvoření zkušebního uživatele ArcGIS Online

Chcete-li povolit uživatele Azure AD k přihlášení do ArcGIS Online, musí být poskytnuty do ArcGIS Online.  
V případě ArcGIS Online zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **ArcGIS** tenanta.

1. Klikněte na tlačítko **pozvání členů**.
   
    ![Pozvání členů](./media/arcgis-tutorial/ic784747.png "zvát členy")

1. Vyberte **automaticky přidat členy bez odeslání e-mailu**a potom klikněte na tlačítko **Další**.
   
    ![Automaticky přidat členy](./media/arcgis-tutorial/ic784748.png "automaticky přidat členy")

1. Na **členy** dialogového okna stránky, proveďte následující kroky:
   
     ![Přidat a zkontrolujte](./media/arcgis-tutorial/ic784749.png "přidat a revize")
    
     a. Zadejte **e-mailu**, **křestní jméno**, a **příjmení** platného účtu AAD, které chcete zřídit.
  
     b. Klikněte na tlačítko **přidat a kontrola**.
1. Zkontrolujte data, která jste zadali a potom klikněte na tlačítko **přidat členy**.
   
    ![Přidat člena](./media/arcgis-tutorial/ic784750.png "přidat člena")
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ArcGIS Online.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ArcGIS online, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ArcGIS Online**.

    ![ArcGIS Online odkaz v seznamu aplikací](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ArcGIS Online na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci ArcGIS Online.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

