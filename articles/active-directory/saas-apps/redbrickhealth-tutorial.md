---
title: 'Kurz: Integrace Azure Active Directory pomocí RedBrick Health | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RedBrick stavu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 11452dfb94a5a1c8cf4734b1ef21a44cccafbea1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430504"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Kurz: Integrace Azure Active Directory se službou RedBrick stavu

V tomto kurzu se dozvíte, jak integrovat RedBrick Health se službou Azure Active Directory (Azure AD).

Integrace s Azure AD RedBrick Health poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k RedBrick stavu.
- Uživatele, aby automaticky získat přihlášení k RedBrick stavu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RedBrick stavu, potřebujete následující položky:

- S předplatným služby Azure AD
- Stav RedBrick jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RedBrick stavu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-redbrick-health-from-the-gallery"></a>Přidání RedBrick stavu z Galerie
Konfigurace integrace RedBrick stavu do služby Azure AD, budete muset přidat RedBrick stavu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat RedBrick stavu z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **RedBrick stavu**vyberte **RedBrick stavu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![RedBrick stavu v seznamu výsledků](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí RedBrick stavu na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v RedBrick stavu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě RedBrick Health potřeba navázat.

Ve službě RedBrick Health, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RedBrick stavu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele RedBrick stavu](#create-a-redbrick-health-test-user)**  – Pokud chcete mít protějšek Britta Simon RedBrick stavu, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci RedBrick stavu.

**Ke konfiguraci Azure AD jednotné přihlašování s RedBrick stavu, postupujte následovně:**

1. Na webu Azure Portal na **RedBrick stavu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

1. Na **RedBrick stavu domény a adresy URL** části, proveďte následující kroky:

    ![RedBrick stavu domény a adresy URL jednotného přihlašování – informace](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `http://www.redbrickhealth.com`
    
    b. V **adresy URL odpovědi** textového pole zadejte adresu URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Pro produkční prostředí: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Klikněte na tlačítko **zobrazit pokročilé nastavení URL**.
    
    ![RedBrick stavu domény a adresy URL jednotného přihlašování – informace](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. V **stav přenosu** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Stav přenosu hodnota není skutečný. Aktualizujte tato hodnota se skutečným stavem Relay. Kontakt [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) tuto výhodu získáte.

1. RedBrick stavu aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Tyto deklarace jsou pro konkrétní zákazníky a závisí na vašich požadavků. Následující volitelné deklarace identity jsou příkladu pouze která můžete nakonfigurovat pro vaši aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci.

    ![Konfigurace jednotného přihlašování](./media/redbrickhealth-tutorial/attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | hlavní název | ********** |
    | ID klienta | ********** |
    | id účastníka | ********** |
    
    > [!NOTE]
    > Tyto hodnoty jsou pouze z důvodu odkazu. Budete muset definovat atributy podle požadavků vaší organizace. Obraťte se prosím na [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) získat další informace o požadované deklarace identit.
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.
    
    ![Konfigurace jednotného přihlašování](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/redbrickhealth-tutorial/tutorial_general_400.png)

1. Na **RedBrick konfigurace stavu** klikněte na tlačítko **konfigurace RedBrick stavu** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace redBrick stavu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **RedBrick stavu** straně, je nutné odeslat na stažený **Certificate(Base64)** a **SAML Entity ID** k [RedBrick stavu tým podpory](https://home.redbrickhealth.com/contact/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/redbrickhealth-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/redbrickhealth-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/redbrickhealth-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-redbrick-health-test-user"></a>Vytvoření zkušebního uživatele RedBrick stavu

V této části vytvořte uživatele Britta Simon v RedBrick stavu. Práce s [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) přidat uživatele na platformě RedBrick stavu. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k RedBrick stavu.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon RedBrick stavu, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **RedBrick stavu**.

    ![Odkaz RedBrick stavu v seznamu aplikací](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici RedBrick stavu na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci RedBrick stavu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

