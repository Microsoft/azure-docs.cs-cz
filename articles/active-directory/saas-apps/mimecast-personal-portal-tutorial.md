---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu osobní Mimecast | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a osobní Mimecast portálu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: f1415a1ddc49f10539915ccf0ce8f95ce7daf321
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051866"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Kurz: Integrace Azure Active Directory pomocí Mimecast osobní portálu

V tomto kurzu se dozvíte, jak integrovat Mimecast osobní portálu Azure Active Directory (Azure AD).

Integrace s Azure AD Portal osobní Mimecast poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k portálu Mimecast osobní.
- Uživatele, aby automaticky získat přihlášení k portálu osobní Mimecast (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí portálu osobní Mimecast, potřebujete následující položky:

- S předplatným služby Azure AD
- Portálu osobní Mimecast jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání portálu osobní Mimecast z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Přidání portálu osobní Mimecast z Galerie
Konfigurovat integraci portálu osobní Mimecast do služby Azure AD, budete muset přidat portál osobní Mimecast z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál osobní Mimecast z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **osobní portál Mimecast**vyberte **osobní portál Mimecast** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Osobní portálu Mimecast v seznamu výsledků](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Mimecast osobní portál na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Mimecast osobní portálu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské osobní portálu Mimecast potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí osobního portálu Mimecast, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele portálu osobní Mimecast](#create-a-mimecast-personal-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon Mimecast osobní portálu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci portál Mimecast osobní.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí portálu osobní Mimecast, proveďte následující kroky:**

1. Na webu Azure Portal na **osobní portál Mimecast** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Na **Mimecast osobní portál domény a adresy URL** části, proveďte následující kroky:

    ![Mimecast osobní portál domény a adresy URL jednotného přihlašování – informace](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jihoafrická republika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Poskytuje        | `https://jer-api.mimecast.com/login/saml`|

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | Oblast  |  Hodnota | 
    | --------------- | --------------- |
    | Evropa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Spojené státy   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Jihoafrická republika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrálie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Poskytuje        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL: 

    | Oblast  |  Hodnota | 
    | --------------- | --------------- | 
    | Evropa          | `https://eu-api.mimecast.com/login/saml`|
    | Spojené státy   | `https://us-api.mimecast.com/login/saml`|
    | Jihoafrická republika    | `https://za-api.mimecast.com/login/saml`|
    | Austrálie       | `https://au-api.mimecast.com/login/saml`|
    | Poskytuje        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Hodnota identifikátoru není skutečný. Identifikátor skutečné zaktualizujte příslušnou hodnotu. Kontakt [tým podpory osobní klienta portálu Mimecast](http://www.mimecast.com/customer-success/technical-support/) má být získána hodnota. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Na **Mimecast osobní konfigurace portálu** klikněte na tlačítko **nakonfigurovat portál osobní Mimecast** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace portálu Mimecast osobní](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. V okně jiné webové prohlížeče Přihlaste se ke Mimecast osobní portálu jako správce.

8. Přejděte na **služby \> aplikací**.
   
    ![Aplikace](./media/mimecast-personal-portal-tutorial/ic794998.png "aplikací")

9. Klikněte na tlačítko **ověřování profilů**.
   
    ![Ověřování profilů](./media/mimecast-personal-portal-tutorial/ic794999.png "ověřování profilů")

10. Klikněte na tlačítko **nový profil ověřování**.
   
    ![Nový profil ověřování](./media/mimecast-personal-portal-tutorial/ic795000.png "nový profil ověřování")

11. V **ověřování profilu** části, proveďte následující kroky:
   
    ![Profil ověření](./media/mimecast-personal-portal-tutorial/ic795001.png "profilu ověření systému")
   
    a. V **popis** textového pole zadejte název pro vaši konfiguraci.
   
    b. Vyberte **vynutit ověřování SAML pro osobní Mimecast portál**.
   
    c. Jako **poskytovatele**vyberte **Azure Active Directory**.
   
    d. V **URL vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.
   
    e. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
   
    f. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    g. Otevřete váš **base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikát zprostředkovatele Identity (Metadata)** textového pole.

    h. Vyberte **povolit jednotné přihlašování na**.
   
    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Vytvoření zkušebního uživatele Mimecast osobní portálu

Chcete-li povolit Azure AD uživatelům přihlásit do portálu osobní Mimecast, musí být zřízená Mimecast osobní portál. V případě Mimecast osobní portál je zřizování úlohu.

Budete muset zaregistrovat k doméně, než budete moct vytvořit uživatele.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **osobní portál Mimecast** jako správce.

2. Přejděte na **adresáře \> interní**.
   
    ![Adresáře](./media/mimecast-personal-portal-tutorial/ic795003.png "adresáře")

3. Klikněte na tlačítko **zaregistrovat novou doménu**.
   
    ![Zaregistrujte novou doménu](./media/mimecast-personal-portal-tutorial/ic795004.png "zaregistrovat nové domény")

4. Po vytvoření nové domény, klikněte na tlačítko **novou adresu**.
   
    ![Nová adresa](./media/mimecast-personal-portal-tutorial/ic795005.png "novou adresu")

5. V dialogovém okně Nový adresy, proveďte následující kroky platný Azure ke zřízení účtu AD:
   
    ![Uložit](./media/mimecast-personal-portal-tutorial/ic795006.png "uložit")
   
    a. V **e-mailovou adresu** textové pole, typ **e-mailovou adresu** uživatele jako datový typ **BrittaSimon@contoso.com**.
    
    b. V **globální název** textové pole, typ **uživatelské jméno** jako **BrittaSimon**.

    c. V **heslo**, a **potvrzení hesla** textová pole, typ **heslo** uživatele.
   
    b. Klikněte na **Uložit**.

>[!NOTE]
>Další nástroje pro tvorbu účtu uživatele Mimecast osobní portálu nebo rozhraní API poskytovaných Mimecast osobní portálu můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na portál Mimecast osobní.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Mimecast osobní portál, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **osobní portál Mimecast**.

    ![Osobní portálu Mimecast odkaz v seznamu aplikací](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Mimecast osobní portál na přístupovém panelu, vám by měl získat automaticky přihlášení k portálu Mimecast osobní aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

