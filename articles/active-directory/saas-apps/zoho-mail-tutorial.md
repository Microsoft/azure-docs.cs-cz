---
title: 'Kurz: Integrace Azure Active Directory se službou Zoho | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: c44eef96bc40e2ccfac9e5dceb106ed2e809dea2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055895"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Kurz: Integrace Azure Active Directory se službou Zoho

V tomto kurzu se dozvíte, jak integrovat Zoho s Azure Active Directory (Azure AD).

Zoho integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zoho.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zoho (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zoho, potřebujete následující položky:

- S předplatným služby Azure AD
- Zoho jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zoho z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zoho-from-the-gallery"></a>Přidání Zoho z Galerie
Konfigurace integrace Zoho do služby Azure AD, budete muset přidat Zoho z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zoho z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zoho**vyberte **Zoho** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zoho v seznamu výsledků](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Zoho podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zoho je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zoho potřeba navázat.

V Zoho, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zoho](#create-a-zoho-test-user)**  – Pokud chcete mít protějšek Britta Simon Zoho, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zoho.

**Ke konfiguraci Azure AD jednotné přihlašování s Zoho, proveďte následující kroky:**

1. Na webu Azure Portal na **Zoho** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Na **Zoho domény a adresy URL** části, proveďte následující kroky:

    ![Zoho domény a adresy URL jednotného přihlašování – informace](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Zoho klienta](https://www.zoho.com/mail/contact.html) tuto výhodu získáte. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/zoho-mail-tutorial/tutorial_general_400.png)

6. Na **Zoho konfigurace** klikněte na tlačítko **nakonfigurovat Zoho** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování, adresy URL pro změnu hesla a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Zoho konfigurace](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Zoho e-mailu.

8. Přejděte **ovládací panely**.
   
    ![Ovládací panely](./media/zoho-mail-tutorial/ic789607.png "ovládací panely")

9. Klikněte na tlačítko **ověřování SAML** kartu.
   
    ![Ověřování SAML](./media/zoho-mail-tutorial/ic789608.png "ověřování SAML")

10. V **podrobnosti o ověřování SAML** části, proveďte následující kroky:
   
    ![Podrobnosti ověřování SAML](./media/zoho-mail-tutorial/ic789609.png "podrobnosti o ověřování SAML")
   
    a. V **přihlašovací adresa URL** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    b. V **odhlašovací adresa URL** vložit do textového pole **odhlašování URL** zkopírovanou z webu Azure portal.
   
    c. V **heslo změnit adresu URL** vložit do textového pole **heslo změnit adresu URL** zkopírovanou z webu Azure portal.
       
    d. Otevření certifikátu kódováním base-64 stáhnout z webu Azure portal v programu Poznámkový blok, zkopírujte obsah ho do schránky a a vložte ho do **PublicKey** textového pole.
   
    e. Jako **algoritmus**vyberte **RSA**.
   
    f. Klikněte na **OK**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zoho-mail-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/zoho-mail-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zoho-mail-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zoho-test-user"></a>Vytvoření zkušebního uživatele Zoho

Chcete-li povolit uživatele Azure AD k přihlášení do e-mailu Zoho, musí být poskytnuty do Zoho e-mailu. V případě Zoho e-mailu zřizování se ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli jiné e-mailu Zoho uživatele účtu nástrojů pro vytváření nebo rozhraní API k dispozici prostřednictvím e-mailu Zoho uživatelským účtům, zřídit AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k vaší **e-mailu Zoho** společnosti serveru jako správce.

2. Přejděte na **ovládací panely \> e-mailu a dokumentace**.

3. Přejděte na **podrobnosti o uživateli \> přidat uživatele**.
   
    ![Přidání uživatele](./media/zoho-mail-tutorial/ic789611.png "přidat uživatele")

4. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
    ![Přidání uživatele](./media/zoho-mail-tutorial/ic789612.png "přidat uživatele")
   
    a. V **křestní jméno** , jako je textové pole, typ křestní jméno uživatele **Britta**.

    b. V **příjmení** , jako je textové pole, typ příjmení uživatele **Simon**.

    c. V **ID e-mailu** , jako je textové pole, typ id e-mailu uživatele **brittasimon@contoso.com**.

    d. V **heslo** textového pole zadejte heslo uživatele.
   
    e. Klikněte na **OK**.  
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem pro potvrzení účtu, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Zoho použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Zoho, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zoho**.

    ![Zoho odkaz v seznamu aplikací](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zoho na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zoho.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

