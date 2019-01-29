---
title: 'Kurz: Integrace Azure Active Directory s EverBridge | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 40645db589409ac80c69f1e7595e20869b08f5d0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194674"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Integrace Azure Active Directory s EverBridge

V tomto kurzu se dozvíte, jak integrovat EverBridge s Azure Active Directory (Azure AD).

EverBridge integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k EverBridge.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k EverBridge (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s EverBridge, potřebujete následující položky:

- Předplatné Azure AD
- EverBridge jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání EverBridge z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-everbridge-from-the-gallery"></a>Přidání EverBridge z Galerie

Konfigurace integrace EverBridge do služby Azure AD, budete muset přidat EverBridge z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat EverBridge z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **EverBridge**vyberte **EverBridge** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![EverBridge v seznamu výsledků](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí EverBridge podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v EverBridge je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v EverBridge potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s EverBridge, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno EverBridge](#creating-an-everbridge-test-user)**  – Pokud chcete mít protějšek Britta Simon EverBridge, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci EverBridge.

**Ke konfiguraci Azure AD jednotné přihlašování s EverBridge, proveďte následující kroky:**

1. Na webu Azure Portal na **EverBridge** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

    >[!NOTE]
    >Je potřeba konfigurace aplikace jako portál správce nebo jako člen portál na obou koncích tedy na webu Azure Portal a Everbridge portálu.

4. Ke konfiguraci **EverBridge** aplikace jako **EverBridge Manager Portal**na **základní konfiguraci SAML** části proveďte následující kroky:

    ![EverBridge domény a adresy URL jednotného přihlašování – informace](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso.everbridge.net/<API_Name>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory EverBridge](mailto:support@everbridge.com) k získání těchto hodnot.

5. Ke konfiguraci **EverBridge** aplikace jako **EverBridge člen portál**na **základní konfiguraci SAML** části, proveďte následující kroky:

    * Pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

        ![EverBridge domény a adresy URL jednotného přihlašování – informace](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

        ![EverBridge domény a adresy URL jednotného přihlašování – informace](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlášení na adrese URL. Kontakt [tým podpory EverBridge](mailto:support@everbridge.com) k získání těchto hodnot.

6. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Na **nastavení EverBridge** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace EverBridge](common/configuresection.png)

8. Chcete-li získat jednotné přihlašování nakonfigurované pro **EverBridge** jako **EverBridge Manager Portal** aplikace, proveďte následující kroky: 
 
9. V jiné okno webového prohlížeče, přihlaste se k EverBridge jako správce.

9. V nabídce v horní části klikněte **nastavení** kartě a vyberte **Single Sign-On** pod **zabezpečení**.
   
    ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. V **název** textového pole zadejte název zprostředkovatele identifikátor (například: název vaší společnosti).
   
    b. V **název rozhraní API** textového pole zadejte název rozhraní API.
   
    c. Klikněte na tlačítko **zvolit soubor** tlačítko Nahrát soubor metadat, který jste si stáhli z webu Azure portal.
   
    d. V umístění Identity SAML, vyberte **identita je v elementu NameIdentifier příkazu subjektu**.
   
    e. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    f. Poskytovatele inicioval žádost o vazbu služby, vyberte **přesměrování protokolu HTTP**.

    g. Klikněte na **Uložit**.

10. Ke konfiguraci jednotného přihlašování na **EverBridge** aplikace jako **EverBridge člen portál**, je nutné odeslat na stažený **kód XML metadat federace** k [ Tým podpory Everbridge](mailto:support@everbridge.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
  
### <a name="creating-an-everbridge-test-user"></a>Vytvoření EverBridge testovacího uživatele

V této části vytvoříte uživatele v Everbridge jako Britta Simon. Práce s [tým podpory EverBridge](mailto:support@everbridge.com) přidat uživatele na platformě Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k EverBridge použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **EverBridge**.

    ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici EverBridge na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci EverBridge.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
