---
title: 'Kurz: Integrace Azure Active Directory s Agiloft | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Agiloft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: aca13814-cdbd-46b8-93dc-1578099c5ee4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: f851bd260c18509a3233bb3401383e0e04ba6259
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813657"
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft"></a>Kurz: Integrace Azure Active Directory s Agiloft

V tomto kurzu se dozvíte, jak integrovat Agiloft s Azure Active Directory (Azure AD).

Agiloft integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Agiloft.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Agiloft (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Agiloft, potřebujete následující položky:

- Předplatné Azure AD
- Agiloft jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Agiloft z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-agiloft-from-the-gallery"></a>Přidání Agiloft z Galerie
Konfigurace integrace Agiloft do služby Azure AD, budete muset přidat Agiloft z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Agiloft z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Agiloft**vyberte **Agiloft** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Agiloft v seznamu výsledků](./media/agiloft-tutorial/tutorial_agiloft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Agiloft podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Agiloft je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Agiloft potřeba navázat.

V Agiloft, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Agiloft, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Agiloft](#create-an-agiloft-test-user)**  – Pokud chcete mít protějšek Britta Simon Agiloft, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Agiloft.

**Ke konfiguraci Azure AD jednotné přihlašování s Agiloft, proveďte následující kroky:**

1. Na webu Azure Portal na **Agiloft** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/agiloft-tutorial/tutorial_agiloft_samlbase.png)

3. Na **Agiloft domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![Agiloft domény a adresy URL jednotného přihlašování – informace](./media/agiloft-tutorial/tutorial_agiloft_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/project/<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/project/<KB_NAME>` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com:443/gui2/spsamlsso?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>` |

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Agiloft domény a adresy URL jednotného přihlašování – informace](./media/agiloft-tutorial/tutorial_agiloft_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Agiloft klienta](https://www.agiloft.com/support-login.htm) k získání těchto hodnot. 

5. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/agiloft-tutorial/tutorial_agiloft_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/agiloft-tutorial/tutorial_general_400.png)
    
7. Na **Agiloft konfigurace** klikněte na tlačítko **nakonfigurovat Agiloft** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Agiloft Configuration](./media/agiloft-tutorial/tutorial_agiloft_configure.png) 

8. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Agiloft jako správce.

9. Klikněte na **nastavení** (v levém podokně) a potom **přístup**.

    ![Agiloft Configuration](./media/agiloft-tutorial/setup1.png) 

10. Klikněte na tlačítko **"Konfigurace SAML 2.0 Single Sign-On"**. 
    
    ![Agiloft Configuration](./media/agiloft-tutorial/setup2.png) 

11. Zobrazí se dialogové okno průvodce. V dialogovém okně klikněte na kartu **"Podrobnostech zprostředkovatele Identity"** a vyplňte následující pole:  
    
    ![Agiloft Configuration](./media/agiloft-tutorial/setup4.png) 

    a. V **Id Entity zprostředkovatele identity / vystavitele** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    b. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. Otevřete váš **certifikáty s kódováním base-64** v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **obsah certifikátu X.509 zprostředkovatel identity k dispozici**  textového pole.

    e. Klikněte na **Dokončit**.


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/agiloft-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/agiloft-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/agiloft-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/agiloft-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-agiloft-test-user"></a>Vytvořit testovacího uživatele Agiloft

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. Neexistuje žádná akce vám v této části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Agiloft použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Agiloft Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Agiloft**.

    ![Odkaz Agiloft v seznamu aplikací](./media/agiloft-tutorial/tutorial_agiloft_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Agiloft na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Agiloft.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/agiloft-tutorial/tutorial_general_01.png
[2]: ./media/agiloft-tutorial/tutorial_general_02.png
[3]: ./media/agiloft-tutorial/tutorial_general_03.png
[4]: ./media/agiloft-tutorial/tutorial_general_04.png

[100]: ./media/agiloft-tutorial/tutorial_general_100.png

[200]: ./media/agiloft-tutorial/tutorial_general_200.png
[201]: ./media/agiloft-tutorial/tutorial_general_201.png
[202]: ./media/agiloft-tutorial/tutorial_general_202.png
[203]: ./media/agiloft-tutorial/tutorial_general_203.png

