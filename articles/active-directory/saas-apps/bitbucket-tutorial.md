---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Bitbucket podle rozlišení GmbH | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH.
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
ms.openlocfilehash: c91f62aa2f47cfab7de22def631a7149ab37ba46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434971"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Bitbucket podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH se službou Azure Active Directory (Azure AD).

Integrace jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAML SSO pro Bitbucket podle rozlišení GmbH.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML pro Bitbucket s rozlišení GmbH, potřebujete následující položky:

- S předplatným služby Azure AD
- SAML SSO pro Bitbucket podle rozlišení GmbH jednotné přihlašování v předplatném povolené

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH z Galerie
Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH do služby Azure AD, budete muset přidat jednotné přihlašování SAML pro Bitbucket rozlišení GmbH z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH**vyberte **jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH v seznamu výsledků](./media/bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Bitbucket podle rozlišení, které GmbH podle testovacího uživatele nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, jaké protějšek uživatele v jednotné přihlašování SAML pro Bitbucket pomocí řešení GmbH je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH musí být vytvořeno.

V SAML SSO pro Bitbucket podle rozlišení GmbH, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Bitbucket podle rozlišení GmbH, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření SAML SSO pro Bitbucket rozlišení GmbH testovací uživatel](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH, která souvisí s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a nakonfigurovat jednotné přihlašování v vaše jednotné přihlašování SAML pro Bitbucket a rozlišení GmbH aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Bitbucket podle rozlišení GmbH, proveďte následující kroky:**

1. Na webu Azure Portal na **jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

1. Na **SAML SSO Bitbucket podle rozlišení GmbH domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![Jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](./media/bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](./media/bitbucket-tutorial/tutorial_bitbucket_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/bitbucket-tutorial/tutorial_general_400.png)
    
1. Přihlašování k SAML SSO pro Bitbucket rozlišení GmbH společnosti lokalita jako správce.

1. Na pravé straně hlavního panelu nástrojů klikněte na tlačítko **nastavení**.

1. Přejděte do části pro účty, klikněte na **SAML SingleSignOn** na řádku nabídek.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

1. Na **stránka Konfigurace modulu plug-in SIngleSignOn SAML**, klikněte na tlačítko **přidat zprostředkovatele identity**. 

    ![Přidání zprostředkovatele identity](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

1. Na **zvolte zprostředkovatele Identity SAML** stránku, proveďte následující kroky:

    ![Zprostředkovatel identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Vyberte **typ zprostředkovatele identity** jako **AZURE AD**.

    b. V **název** textového pole zadejte název.

    c. V **popis** textového pole zadejte popis.

    d. Klikněte na **Další**.

1. Na **stránka Konfigurace zprostředkovatele Identity**, klikněte na tlačítko **Další**.

    ![Konfigurace identity](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

1.  Na **Import metadat zprostředkovatele identity SAML** klikněte na stránce **načíst soubor** nahrát **soubor XML s METADATY** soubor, který jste si stáhli z webu Azure portal.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
1. Klikněte na **Další**.

1. Klikněte na tlačítko **uložit nastavení**.

    ![Uložení](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/bitbucket-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/bitbucket-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/bitbucket-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/bitbucket-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Vytvoření SAML SSO pro Bitbucket podle rozlišení GmbH testovacího uživatele

Cílem této části je pro vytvoření uživatele volány Britta Simon v SAML SSO pro Bitbucket rozlišení GmbH. Jednotné přihlašování SAML Bitbucket podle rozlišení GmbH podporuje zřizování v čase a také uživatelům lze vytvořit ručně, obraťte se na [tým podpory jednotného přihlašování SAML pro Bitbucket podle rozlišení GmbH klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) podle vašich požadavků.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon pomocí Azure jednotné přihlašování pomocí řešení GmbH udělení přístupu k jednotné přihlašování SAML pro Bitbucketu.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH**.

    ![Jednotné přihlašování SAML pro Bitbucket podle propojení GmbH řešení v seznamu aplikací](./media/bitbucket-tutorial/tutorial_bitbucket_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



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

