---
title: 'Kurz: Integrace Azure Active Directory se službou AirWatch | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f3bbcbb70759e7a995797cf89ad75a2a39314927
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048476"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Kurz: Integrace Azure Active Directory se službou AirWatch

V tomto kurzu se dozvíte, jak integrovat AirWatch s Azure Active Directory (Azure AD).

AirWatch integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AirWatch
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AirWatch (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AirWatch, potřebujete následující položky:

- S předplatným služby Azure AD
- AirWatch jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AirWatch z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-airwatch-from-the-gallery"></a>Přidání AirWatch z Galerie
Konfigurace integrace AirWatch do služby Azure AD, budete muset přidat AirWatch z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat AirWatch z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AirWatch**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. Na panelu výsledků vyberte **AirWatch**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s AirWatch podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v AirWatch je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v AirWatch potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v AirWatch.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AirWatch, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele AirWatch](#creating-a-airwatch-test-user)**  – Pokud chcete mít protějšek Britta Simon AirWatch, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci AirWatch.

**Ke konfiguraci Azure AD jednotné přihlašování s AirWatch, proveďte následující kroky:**

1. Na webu Azure Portal na **AirWatch** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Na **AirWatch domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. V **identifikátor** textového pole zadejte jako hodnotu `AirWatch`

    > [!NOTE] 
    > Tato hodnota není reálné. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory AirWatch klienta](http://www.air-watch.com/company/contact-us/) tuto výhodu získáte. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Na **AirWatch konfigurace** klikněte na tlačítko **nakonfigurovat AirWatch** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. V okně jiné webové prohlížeče Přihlaste se k webu firmy AirWatch jako správce.

8. V levém navigačním podokně klikněte na tlačítko **účty**a potom klikněte na tlačítko **správci**.
   
   ![Správci](./media/airwatch-tutorial/ic791920.png "správci")

9. Rozbalte **nastavení** nabídky a pak klikněte na tlačítko **Directory Services**.
   
   ![Nastavení](./media/airwatch-tutorial/ic791921.png "nastavení")

10. Klikněte na tlačítko **uživatele** kartě **Base DN** textového pole zadejte název domény a pak klikněte na tlačítko **Uložit**.
   
   ![Uživatel](./media/airwatch-tutorial/ic791922.png "uživatele")

11. Klikněte na tlačítko **Server** kartu.
   
   ![Server](./media/airwatch-tutorial/ic791923.png "serveru")

12. Proveďte následující kroky:
    
    ![Nahrát](./media/airwatch-tutorial/ic791924.png "nahrát")   
    
    a. Jako **typ adresáře**vyberte **žádný**.

    b. Vyberte **používat protokol SAML pro ověřování**.

    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **nahrát**.

13. V **žádosti** části, proveďte následující kroky:
    
    ![Požádat o](./media/airwatch-tutorial/ic791925.png "žádosti")  

    a. Jako **typ vazby žádosti**vyberte **příspěvek**.

    b. Na webu Azure Portal na **nakonfigurovat jednotné přihlašování v Airwatch** dialogového okna stránky, zkopírujte **SAML jednotné přihlašování – adresa URL služby** hodnotu a vložte jej do **Identity poskytovatele jednotného přihlašování Adresa URL** textového pole.

    c. Jako **formátem**vyberte **e-mailovou adresu**.

    d. Klikněte na **Uložit**.

14. Klikněte na tlačítko **uživatele** kartu znovu.
    
    ![Uživatel](./media/airwatch-tutorial/ic791926.png "uživatele")

15. V **atribut** části, proveďte následující kroky:
    
    ![Atribut](./media/airwatch-tutorial/ic791927.png "atribut")

    a. V **identifikátor objektu** textové pole, typ **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. V **uživatelské jméno** textové pole, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. V **zobrazovaný název** textové pole, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. V **křestní jméno** textové pole, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. V **příjmení** textové pole, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. V **e-mailu** textové pole, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Klikněte na **Uložit**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** Britta Simon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-airwatch-test-user"></a>Vytvoření zkušebního uživatele AirWatch

Umožňuje uživatelům Azure AD se přihlaste k AirWatch, musí být poskytnuty v k AirWatch.

* Při zřizování AirWatch, je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **AirWatch** společnosti serveru jako správce.
2. V navigačním podokně na levé straně klikněte na tlačítko **účty**a potom klikněte na tlačítko **uživatelé**.
   
   ![Uživatelé](./media/airwatch-tutorial/ic791929.png "uživatelů")
3. V **uživatelé** nabídky, klikněte na tlačítko **zobrazení seznamu**a potom klikněte na tlačítko **přidat \> přidat uživatele**.
   
   ![Přidání uživatele](./media/airwatch-tutorial/ic791930.png "přidat uživatele")
4. Na **přidat / upravit uživatele** dialogového okna, proveďte následující kroky:

   ![Přidání uživatele](./media/airwatch-tutorial/ic791931.png "přidat uživatele")   
   1. Typ **uživatelské jméno**, **heslo**, **potvrzení hesla**, **křestní jméno**, **příjmení**,  **E-mailová adresa** platného účtu služby Azure Active Directory ke zřízení do související textových polí.
   2. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné AirWatch uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných AirWatch uživatelským účtům, zřídit AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k AirWatch použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit AirWatch Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **AirWatch**.

    ![Konfigurace jednotného přihlašování](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png

