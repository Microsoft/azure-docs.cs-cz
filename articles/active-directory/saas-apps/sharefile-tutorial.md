---
title: 'Kurz: Integrace Azure Active Directory se službou Citrix ShareFile | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9919be128ae651b589a37f957cc59ce6d171143f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431728"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Kurz: Integrace Azure Active Directory se službou Citrix ShareFile

V tomto kurzu se dozvíte, jak Citrix ShareFile integrovat s Azure Active Directory (Azure AD).

Citrix ShareFile integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Citrix ShareFile.
- Uživatele, aby automaticky získat přihlášení k Citrix ShareFile (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Citrix ShareFile, potřebujete následující položky:

- S předplatným služby Azure AD
- Citrix ShareFile jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Citrix ShareFile přidat z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-citrix-sharefile-from-the-gallery"></a>Citrix ShareFile přidat z Galerie
Konfigurace integrace Citrix ShareFile do služby Azure AD, budete muset přidat Citrix ShareFile z Galerie na váš seznam spravovaných aplikací SaaS.

**Citrix ShareFile přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Citrix ShareFile**vyberte **Citrix ShareFile** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Citrix ShareFile v seznamu výsledků](./media/sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Citrix ShareFile podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Citrix ShareFile je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Citrix ShareFile musí být vytvořeno.

V Citrix ShareFile přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Citrix ShareFile, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  – Pokud chcete mít protějšek Britta Simon Citrix ShareFile, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Citrix ShareFile.

**Ke konfiguraci Azure AD jednotné přihlašování s Citrix ShareFile, proveďte následující kroky:**

1. Na webu Azure Portal na **Citrix ShareFile** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/sharefile-tutorial/tutorial_sharefile_samlbase.png)

1. Na **Citrix ShareFile domény a adresy URL** části, proveďte následující kroky:

    ![Citrix ShareFile domény a adresy URL jednotného přihlašování – informace](./media/sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.sharefile.com/saml/login`

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory Citrix ShareFile klienta](https://www.citrix.co.in/products/sharefile/support.html) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/sharefile-tutorial/tutorial_sharefile_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/sharefile-tutorial/tutorial_general_400.png)

1. Na **konfigurace systému Citrix ShareFile** klikněte na tlačítko **konfigurace Citrix ShareFile** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Citrix ShareFile konfigurace](./media/sharefile-tutorial/tutorial_sharefile_configure.png)

1. V okně jiné webové prohlížeče, přihlaste se do vaší **Citrix ShareFile** společnosti serveru jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správce**.

1. V levém navigačním podokně vyberte **konfigurovat Single Sign-On**.
   
    ![Účet správy](./media/sharefile-tutorial/ic773627.png "účet správy")

1. Na **Single Sign-On / konfigurace SAML 2.0** dialogového okna stránky v části **základní nastavení**, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/sharefile-tutorial/ic773628.png "jednotného přihlašování")
   
    a. Klikněte na tlačítko **povolit SAML**.
    
    b. V **vašeho zprostředkovatele identity vystavitele / Entity ID** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    c. Klikněte na tlačítko **změnu** vedle **certifikát X.509** pole a pak nahrajte certifikát, který jste si stáhli z webu Azure portal.
    
    d. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
    
    e. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

1. Klikněte na tlačítko **Uložit** na portálu pro správu Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sharefile-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/sharefile-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sharefile-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sharefile-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Vytvoření zkušebního uživatele Citrix ShareFile

Chcete-li povolit uživatele Azure AD k přihlášení do Citrix ShareFile, musí být poskytnuty do Citrix ShareFile. V případě Citrix ShareFile zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Citrix ShareFile** tenanta.

1. Klikněte na tlačítko **spravovat uživatele \> spravovat domácí uživatelé \> + vytvořit zaměstnance**.
   
   ![Vytvoření zaměstnance](./media/sharefile-tutorial/IC781050.png "vytvoření zaměstnance")

1. Na **základní informace** části, proveďte následující kroky:
   
   ![Základní informace](./media/sharefile-tutorial/IC799951.png "základní informace")
   
   a. V **e-mailovou adresu** textového pole zadejte e-mailovou adresu Britta Simon jako **brittasimon@contoso.com**.
   
   b. V **křestní jméno** textové pole, typ **křestní jméno** uživatele jako **Britta**.
   
   c. V **příjmení** textové pole, typ **příjmení** uživatele jako **Simon**.

1. Klikněte na tlačítko **přidat uživatele**.
  
   >[!NOTE]
   >Držitel účtu Azure AD bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní. Další nástroje pro tvorbu Citrix ShareFile uživatelského účtu nebo rozhraní API poskytovaných Citrix ShareFile můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Citrix ShareFile.

![Přiřazení role uživatele][200] 

**Citrix ShareFile přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Citrix ShareFile**.

    ![Citrix ShareFile odkaz v seznamu aplikací](./media/sharefile-tutorial/tutorial_sharefile_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Citrix ShareFile na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Citrix ShareFile.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharefile-tutorial/tutorial_general_01.png
[2]: ./media/sharefile-tutorial/tutorial_general_02.png
[3]: ./media/sharefile-tutorial/tutorial_general_03.png
[4]: ./media/sharefile-tutorial/tutorial_general_04.png

[100]: ./media/sharefile-tutorial/tutorial_general_100.png

[200]: ./media/sharefile-tutorial/tutorial_general_200.png
[201]: ./media/sharefile-tutorial/tutorial_general_201.png
[202]: ./media/sharefile-tutorial/tutorial_general_202.png
[203]: ./media/sharefile-tutorial/tutorial_general_203.png
