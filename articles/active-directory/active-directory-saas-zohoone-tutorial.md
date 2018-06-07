---
title: 'Kurz: Azure Active Directory integrace s Zoho jeden | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zoho jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 3ffabecad5cd82380d7f05d48761bd8f1e430b37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654765"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Kurz: Azure Active Directory integrace s Zoho jeden

V tomto kurzu zjistěte, jak integrovat Zoho jeden s Azure Active Directory (Azure AD).

Integrace s Azure AD Zoho jeden poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Zoho jeden.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zoho jeden (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zoho jeden, potřebujete následující položky:

- Předplatné služby Azure AD
- Jednu Zoho jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zoho jeden z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zoho-one-from-the-gallery"></a>Přidání Zoho jeden z Galerie
Chcete-li nakonfigurovat integraci Zoho jeden do služby Azure AD, přidejte Zoho jeden z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Zoho jeden z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zoho jeden**, vyberte **Zoho jeden** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Zoho jeden v seznamu výsledků](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho jeden podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zoho jeden je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zoho jeden musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zoho jeden, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zoho jeden](#create-a-zoho-one-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zoho jednu propojenou s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v Zoho jednu aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Zoho jeden, proveďte následující kroky:**

1. Na portálu Azure na **Zoho jeden** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. Na **Zoho jednu doménu a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Zoho jednu doménu a adresy URL jednotné přihlašování informace](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_url.png)

    a. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL: `one.zoho.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**.

    d. V **předávání stavu** textovému poli, zadejte adresu URL:`https://one.zoho.com`

4. Pokud chcete nakonfigurovat aplikace **SP** initiated režimu provést následující krok:

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Podle předchozích **adresa URL odpovědi** a **přihlašovací adresa URL** hodnota není skutečné. Aktualizujte hodnotu s skutečná adresa URL odpovědi a přihlašování adresy URL, která se vysvětluje dále v tomto kurzu. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-zohoone-tutorial/tutorial_general_400.png)
    
7. Na **jednu konfiguraci Zoho** klikněte na tlačítko **nakonfigurovat jeden Zoho** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace Zoho jeden](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se k vaší Zoho jedné lokalitě společnosti jako správce.

9. Na **organizace** kartě, klikněte na **instalační program** v části **ověřování SAML**.

    ![Jeden Zoho org](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_setup.png)

10. Na stránce automaticky otevírané okno proveďte následující kroky:

    ![Jeden Zoho sig](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_save.png)

    a. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    b. V **Sign-out URL** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    c. Klikněte na tlačítko **Procházet** nahrát **certifikátu (Base64)** který jste si stáhli z portálu Azure.

    d. Klikněte na **Uložit**.

11. Po uložení nastavení ověřování SAML, zkopírujte **SAML Identfier** hodnotu a použít tuto hodnotu v **adresa URL odpovědi** na portálu Azure v části **Zoho jednu doménu a adresy URL** oddíl.

    ![Jeden Zoho saml](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Přejděte na **domén** a pak klikněte **přidat doménu**.

    ![Zoho jednu doménu](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_domain.png)

13. Na **přidáním domény** proveďte následující kroky:

    ![Jeden Zoho přidání domény](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. V **název domény** textovému poli, typ domény jako **contoso.com**.

    b. Klikněte na tlačítko **Add** (Přidat).

    >[!Note]
    >Po přidání domény postupujte podle [tyto](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroky k ověření vaší domény. Jakmile doména je verfified, použijte název domény v **přihlašovací adresa URL** v **Zoho jednu doménu a adresy URL** části na portálu Azure.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-zohoone-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-zohoone-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-zohoone-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-zohoone-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zoho-one-test-user"></a>Vytvoření jednoho Zoho zkušebního uživatele

Povolit uživatelům Azure AD přihlášení k Zoho jeden, musí být zřízená do Zoho jeden. V jedné Zoho zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k Zoho jeden jako správce zabezpečení.

2. Na **uživatelé** kartě, klikněte na **uživatele logo**.

    ![Zoho jednoho uživatele](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_users.png)

3. Na **přidat uživatele** proveďte následující kroky:

    ![Jeden Zoho přidat uživatele](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. V **název** textové pole, zadejte jméno uživatele, jako je **Britta simon**.
    
    b. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    >[!Note]
    >Vyberte ze seznamu domény ověřené domény.

    c. Klikněte na tlačítko **Add** (Přidat).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zoho jeden.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zoho jeden, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zoho jeden**.

    ![V seznamu aplikací na Zoho jeden odkaz](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zoho jeden na přístupovém panelu, jste měli získat automaticky přihlášení k Zoho jedné aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_203.png

