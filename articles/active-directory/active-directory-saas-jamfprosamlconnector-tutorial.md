---
title: 'Kurz: Azure Active Directory integrace s Jamf Pro | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 488787adfbae0147c3bd0425d839e2ad8c5ed786
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2018
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Kurz: Azure Active Directory integrace s Jamf Pro

V tomto kurzu zjistěte, jak Jamf Pro integraci s Azure Active Directory (Azure AD).

Jamf Pro integraci s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Jamf Pro.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Jamf Pro (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jamf Pro, potřebujete následující položky:

- Předplatné služby Azure AD
- Jamf Pro jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jamf Pro z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jamf-pro-from-the-gallery"></a>Přidání Jamf Pro z Galerie
Při konfiguraci integrace Jamf Pro do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Jamf Pro z galerie.

**Pokud chcete přidat Jamf Pro z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Jamf Pro**, vyberte **Jamf Pro** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Jamf Pro v seznamu výsledků](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Jamf Pro podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Jamf Pro je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské ve Jamf Pro musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jamf Pro, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Jamf Pro](#create-a-jamf-pro-test-user)**  – Pokud chcete mít protějšek Britta Simon ve Jamf Pro propojenou s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Jamf Pro.

**Chcete-li nakonfigurovat Jamf Pro Azure AD jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure na **Jamf Pro** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Na **Jamf Pro domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Pro doménu Jamf a adresy URL jednotné přihlašování informace](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Pro doménu Jamf a adresy URL jednotné přihlašování informace](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Zobrazí se skutečná hodnota identifikátoru z **jednotné přihlašování** části portálu Jamf Pro, který je vysvětlen později v tomto kurzu. Můžete rozbalit skutečnou **subdomény** z hodnota identifikátoru hodnoty a použít jej **subdomény** informace v přihlašovací adresa URL a adresa URL odpovědi.

5. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Jamf Pro.

8. Klikněte na **ikonu nastavení** z pravém horním rohu stránky.

    ![Konfigurace Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Klikněte na **jednotné přihlašování**.

    ![Konfigurace Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Projděte dolů maximálně **zprostředkovatele IDENTITY** pod **jednotné přihlašování** části a proveďte následující kroky:

    ![Konfigurace Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. Vyberte **jiných** jako možnost z **zprostředkovatele IDENTITY** rozevíracího seznamu.

    b. V **ostatní poskytovatele** textovému poli, zadejte **Azure AD**.

    c. Vyberte **adresu URL metadat** jako možnost z **IDENTITY zprostředkovatele METADAT zdroje** rozevíracího seznamu a do textového pole následující vložte **adresu Url aplikace federační Metadata** hodnotu, která jste zkopírovali z portálu Azure.

    d. Kopírování **Entity ID** vlaue a vložte ji do **identifikátoru (Entity ID)** textového pole v **Jamf Pro domény a adresy URL** části na portálu Azure.

    >[!NOTE]
    > Zde `aadsso` je součástí subdomény, (což je pro referenční účely). Tato hodnota slouží k dokončení přihlašovací adresa URL a adresa URL odpovědi v **Jamf Pro domény a adresy URL** části na portálu Azure.

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-jamf-pro-test-user"></a>Vytvoření zkušebního uživatele Jamf Pro

Povolit uživatelům přihlášení do Jamf Pro Azure AD, musí být zřízená do Jamf Pro. V případě Jamf Pro zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k webu Jamf Pro společnosti jako správce.

2. Klikněte na **ikonu nastavení** z pravém horním rohu stránky.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Klikněte na **Jamf Pro uživatelské účty a skupiny**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Klikněte na možnost **Nové**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Vyberte **vytvořit standardní účet**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. Na **nový účet** dailog, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. V **uživatelské jméno** textovému poli, zadejte úplný název BrittaSimon.

    b. Vyberte požadované možnosti podle vaší organizace pro **úroveň přístupu**, **nastavit oprávnění**a pro **stav přístupu**.
    
    c. V **úplný název** textovému poli, zadejte úplný název Britta Simon.

    d. V **e-MAILOVOU adresu** textovému poli, zadejte e-mailovou adresu účtu Britta Simon.

    e. V **heslo** textovému poli, zadejte heslo uživatele.

    f. V **ověřit heslo** textovému poli, zadejte heslo uživatele.

    g. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v Jamf Pro udělení přístupu.

![Přiřadit role uživatele][200] 

**Chcete Jamf Pro přiřadit Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Jamf Pro**.

    ![Odkaz Jamf Pro v seznamu aplikací](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Jamf Pro na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Jamf Pro.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png

