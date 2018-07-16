---
title: 'Kurz: Integrace Azure Active Directory s vymazat revizí | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vymazat revize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 6ce6661bf6d3841f7ade78a74d50a1d6eeefbdaf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047990"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Kurz: Integrace Azure Active Directory s vymazat revize

V tomto kurzu se dozvíte, jak vymazat revize integrovat s Azure Active Directory (Azure AD).

Vymazat revize integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k revizi vymazat.
- Uživatele, aby automaticky získat přihlášení k vymazat revize (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vymazat revize, potřebujete následující položky:

- S předplatným služby Azure AD
- Vymazat revize jednotné přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání kontroly vymazat z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-clear-review-from-the-gallery"></a>Přidání kontroly vymazat z Galerie
Pokud chcete nakonfigurovat integraci vymazat revize do služby Azure AD, budete muset vymazat revize přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Vymazat revize přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vymazat revize**vyberte **vymazat revize** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vymazat zkontrolujte v seznamu výsledků](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí vymazat revizi na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek vymazat reviduje je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské reviduje vymazat.

Vymazat reviduje přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vymazat kontroly, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele vymazat revize](#create-a-clear-review-test-user)**  – Pokud chcete mít protějšek Britta Simon reviduje jasné, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci vymazat revize.

**Ke konfiguraci Azure AD jednotné přihlašování s vymazat revizi, proveďte následující kroky:**

1. Na webu Azure Portal na **vymazat revize** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. Na **vymazat revize domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **zprostředkovatele identity iniciované** režimu:

    ![Vymazat revize domény a adresy URL jednotného přihlašování – informace](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<customer name>.clearreview.com/sso/metadata/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<customer name>.clearreview.com/sso/acs/`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Vymazat revize domény a adresy URL jednotného přihlašování – informace](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory vymazat kontroly](https://clearreview.com/contact/) k získání těchto hodnot.

5. Vymazat revizi aplikace očekávat, že hodnota identifikátoru jedinečných uživatelů v deklaraci identity identifikátor názvu. Byste měli namapovat hodnotu identifikátoru uživatele **user.mail**.

    ![V části atribut](./media/clearreview-tutorial/attribute.png)


6. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clearreview-tutorial/tutorial_general_400.png)

8. Na **vymazat konfiguraci revize** klikněte na tlačítko **konfigurace vymazat revize** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL, SAML Entity ID a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Vymazat konfiguraci revize](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

9. Ke konfiguraci jednotného přihlašování na **vymazat revize** straně, otevřete **vymazat revize** portálu pomocí přihlašovacích údajů správce.

10. Vyberte **správce** z levé navigace.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

11. Vyberte **změnu** v dolní části stránky.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

12. Proveďte následující kroky **nastavení jednotného přihlašování** stránky

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. V **URL vystavitele** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    b. V **koncový bod SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.    

    c. V **koncový bod objektu SLO** textového pole vložte hodnotu **přihlašovací adresa URL služby** zkopírovanou z webu Azure portal. 

    d. Otevřete stažený certifikát v programu Poznámkový blok a vložte obsah **certifikát X.509** textového pole.   

13. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/clearreview-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/clearreview-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/clearreview-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/clearreview-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-clear-review-test-user"></a>Vytvoření zkušebního uživatele vymazat revize

V této části vytvořte uživatele Britta Simon reviduje vymazat. Prosím práci s [tým podpory vymazat kontroly](https://clearreview.com/contact/) přidat uživatele na platformě vymazat revize.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k revizi vymazat.

![Přiřazení role uživatele][200] 

**Chcete vymazat revize přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **vymazat revize**.

    ![Vymazat revize odkazu v seznamu aplikací](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici vymazat kontrolu na přístupovém panelu, vám by měl získat automaticky přihlášení k vymazat revizi aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
