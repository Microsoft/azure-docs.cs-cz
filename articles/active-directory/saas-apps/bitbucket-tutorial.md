---
title: 'Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML pro Bitbucket podle řešení GmbH | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Bitbucket podle řešení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: d18d41caf7a7fb3a821c388e786b3a45d30f4810
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221440"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML pro Bitbucket podle řešení GmbH

V tomto kurzu zjistěte, jak integrovat jednotné přihlašování SAML pro Bitbucket podle řešení GmbH službou Azure Active Directory (Azure AD).

Integrace jednotné přihlašování SAML pro Bitbucket podle řešení GmbH s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k jednotné přihlašování SAML pro Bitbucket podle řešení GmbH.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k jednotné přihlašování SAML pro Bitbucket podle řešení GmbH (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí jednotného přihlašování SAML pro Bitbucket pomocí řešení GmbH, potřebujete následující položky:

- Předplatné služby Azure AD
- Jednotné přihlašování SAML pro Bitbucket podle řešení GmbH jednotné přihlašování v předplatném povolené

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotné přihlašování SAML pro Bitbucket podle řešení GmbH z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotné přihlašování SAML pro Bitbucket podle řešení GmbH z Galerie
Při konfiguraci integrace jednotné přihlašování SAML pro Bitbucket podle řešení GmbH do služby Azure AD, potřebujete přidat jednotné přihlašování SAML pro Bitbucket řešení GmbH z Galerie si na seznam spravovaných aplikací SaaS.

**Přidání jednotné přihlašování SAML pro Bitbucket řešení GmbH z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Bitbucket podle řešení GmbH**, vyberte **jednotné přihlašování SAML pro Bitbucket podle řešení GmbH** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![Jednotné přihlašování SAML pro Bitbucket pomocí řešení GmbH v seznamu výsledků](./media/bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Bitbucket pomocí řešení, které GmbH podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v jednotné přihlašování SAML pro Bitbucket pomocí řešení GmbH je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v jednotné přihlašování SAML pro Bitbucket pomocí řešení GmbH musí být vytvořeno.

V jednotné přihlašování SAML pro Bitbucket podle řešení GmbH, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Bitbucket podle řešení GmbH, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření jednotné přihlašování SAML pro Bitbucket uživatelem testovací GmbH řešení](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro Bitbucket podle řešení GmbH propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší jednotné přihlašování SAML pro Bitbucket podle řešení GmbH aplikace.

**Konfigurace Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Bitbucket pomocí řešení GmbH, proveďte následující kroky:**

1. Na portálu Azure na **jednotné přihlašování SAML pro Bitbucket podle řešení GmbH** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

3. Na **jednotné přihlašování SAML Bitbucket podle řešení GmbH domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![Jednotné přihlašování SAML pro Bitbucket podle řešení GmbH domény a adresy URL pro jednotné přihlašování informace](./media/bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Jednotné přihlašování SAML pro Bitbucket podle řešení GmbH domény a adresy URL pro jednotné přihlašování informace](./media/bitbucket-tutorial/tutorial_bitbucket_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory jednotné přihlašování SAML pro Bitbucket podle řešení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/bitbucket-tutorial/tutorial_general_400.png)
    
7. Přihlášení k vaší jednotné přihlašování SAML pro Bitbucket řešení GmbH společnosti lokalita jako správce.

8. Na pravé straně na hlavním panelu nástrojů, klikněte na tlačítko **nastavení**.

9. Přejděte k části účty, klikněte na **SAML SingleSignOn** na řádek nabídek.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

10. Na **stránku konfigurace modulu plug-in SIngleSignOn SAML**, klikněte na tlačítko **přidat idp**. 

    ![Rozšíření idp přidat](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

11. Na **zvolte poskytovatele Identity SAML** stránky, proveďte následující kroky:

    ![Poskytovatel identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Vyberte **Idp typ** jako **AZURE AD**.

    b. V **název** textovému poli, zadejte název.

    c. V **popis** textovému poli, zadejte popis.

    d. Klikněte na **Další**.

12. Na **stránku Konfigurace zprostředkovatele Identity**, klikněte na tlačítko **Další**.

    ![Konfigurace identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

13.  Na **importovat Idp Metadata SAML** stránky, klikněte na tlačítko **načíst soubor** nahrát **soubor XML s METADATY** souboru, který jste si stáhli z portálu Azure.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
14. Klikněte na **Další**.

15. Klikněte na tlačítko **uložit nastavení**.

    ![Uložení](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/bitbucket-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/bitbucket-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/bitbucket-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/bitbucket-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Vytvoření jednotné přihlašování SAML pro Bitbucket řešení GmbH testovací uživatel

Cílem této části je vytvoření uživatele volá Britta Simon v jednotné přihlašování SAML pro Bitbucket řešení GmbH. Jednotné přihlašování SAML pro Bitbucket podle řešení GmbH podporuje zřizování za běhu a také uživatelů lze vytvořit ručně, obraťte se na [tým podpory jednotné přihlašování SAML pro Bitbucket podle řešení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) podle potřeby.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotné přihlašování pomocí řešení GmbH udělení přístupu jednotné přihlašování SAML pro Bitbucket.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon jednotné přihlašování SAML pro Bitbucket podle řešení GmbH, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **jednotné přihlašování SAML pro Bitbucket podle řešení GmbH**.

    ![Jednotné přihlašování SAML pro Bitbucket podle propojení GmbH řešení v seznamu aplikací](./media/bitbucket-tutorial/tutorial_bitbucket_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko jednotné přihlašování SAML pro Bitbucket pomocí dlaždice GmbH řešení na přístupovém panelu jste měli získat automaticky přihlášení k vaší jednotné přihlašování SAML pro Bitbucket podle řešení GmbH aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitbucket-tutorial/tutorial_general_01.png
[2]: ./media/bitbucket-tutorial/tutorial_general_02.png
[3]: ./media/bitbucket-tutorial/tutorial_general_03.png
[4]: ./media/bitbucket-tutorial/tutorial_general_04.png

[100]: ./media/bitbucket-tutorial/tutorial_general_100.png

[200]: ./media/bitbucket-tutorial/tutorial_general_200.png
[201]: ./media/bitbucket-tutorial/tutorial_general_201.png
[202]: ./media/bitbucket-tutorial/tutorial_general_202.png
[203]: ./media/bitbucket-tutorial/tutorial_general_203.png

