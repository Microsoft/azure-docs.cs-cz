---
title: 'Kurz: Integrace Azure Active Directory s vystavovací Hudba | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Settling music.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 4a4d4fa704381ed9ab7c79c6ad0f6196a9ac37f2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040367"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Kurz: Integrace Azure Active Directory s vystavovací Hudba

V tomto kurzu se dozvíte, jak integrovat Settling music se službou Azure Active Directory (Azure AD).

Hudba Settling integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vyrovnání music.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k vyrovnání music (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vystavovací Hudba, potřebujete následující položky:

- S předplatným služby Azure AD
- Settling Hudba jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidávání hudební Settling z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-settling-music-from-the-gallery"></a>Přidávání hudební Settling z Galerie
Ke konfiguraci integrace Settling Hudba do služby Azure AD, budete muset přidat Settling Hudba v galerii na váš seznam spravovaných aplikací SaaS.

**Hudba Settling přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vystavovací Hudba**vyberte **vystavovací Hudba** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vyrovnání Hudba v seznamu výsledků](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s vystavovací Hudba podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Settling Hudba je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v vystavovací music.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Settling Hudba, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Settling Hudba](#create-a-settling-music-test-user)**  – Pokud chcete mít protějšek Britta Simon vystavovací Hudba, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování aplikace music Settling.

**Ke konfiguraci Azure AD jednotné přihlašování s vystavovací Hudba, proveďte následující kroky:**

1. Na webu Azure Portal na **vystavovací Hudba** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

3. Na **vystavovací Hudba domény a adresy URL** části, proveďte následující kroky:

    ![Vyrovnání Hudba domény a adresy URL jednotné přihlašování – informace](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [vystavovací tým podpory klienta Hudba](https://rakurakuseisan.jp/) k získání těchto hodnot. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/settlingmusic-tutorial/tutorial_general_400.png)

6. Na **vystavovací Hudba konfigurace** klikněte na tlačítko **nakonfigurovat vyrovnání Hudba** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Vyrovnání Hudba konfigurace](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

7. V jiné okno webového prohlížeče, přihlaste se k vyrovnání Hudba jako správce zabezpečení.

8. Na stránce klikněte na tlačítko **správu** kartu.

    ![Vyrovnání hudba stažená v kroku 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

9. Klikněte na **nastavení systému** kartu.

    ![Krok 2 Hudba vyrovnání](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

10. Přepnout na **zabezpečení** kartu.

    ![Vyrovnání krok3 Hudba](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

11. Na **jednotné přihlašování – nastavení** části, proveďte následující kroky:

    ![Vyrovnání step5 Hudba](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Klikněte na tlačítko **umožňující**.

    b. V **přihlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele ID** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    d. Klikněte na tlačítko **zvolit soubor** k nahrání **certifikát (Base64)** který jste si stáhli formuláře webu Azure portal.

    e. Klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/settlingmusic-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/settlingmusic-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/settlingmusic-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-settling-music-test-user"></a>Vytvoření zkušebního uživatele Settling Hudba

V této části vytvořte uživatele Britta Simon v Settling music. Práce s [vystavovací tým podpory klienta Hudba](https://rakurakuseisan.jp/) přidat uživatele na platformě Settling music. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k vyrovnání music.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Settling hudby, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **vystavovací Hudba**.

    ![Odkaz Settling Hudba v seznamu aplikací](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Settling hudby na přístupovém panelu, vám by měl získat automaticky přihlášení k Settling aplikace music.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

