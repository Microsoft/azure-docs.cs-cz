---
title: 'Kurz: Integrace Azure Active Directory se službou DocuSign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 6b2b5f72c9520498d4834bbbfaf6c56656a807e7
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015209"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Kurz: Integrace Azure Active Directory se službou DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Azure Active Directory (Azure AD).

Integrace DocuSign s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k DocuSign.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k DocuSign (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD službou DocuSign, potřebujete následující položky:

- Předplatné Azure AD
- DocuSign jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání DocuSign z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie

Konfigurace integrace DocuSign do služby Azure AD, budete muset přidat DocuSign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat DocuSign z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **DocuSign**vyberte **DocuSign** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![DocuSign v seznamu výsledků](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí DocuSign podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v DocuSign je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v DocuSign.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování službou DocuSign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele DocuSign](#creating-a-docusign-test-user)**  – Pokud chcete mít protějšek Britta Simon v DocuSign, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci DocuSign.

**Ke konfiguraci Azure AD jednotného přihlašování službou DocuSign, proveďte následující kroky:**

1. Na webu Azure Portal na **DocuSign** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![DocuSign domény a adresy URL jednotného přihlašování – informace](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později **zobrazit SAML 2.0 koncové body** části v tomto kurzu.

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Na **nastavení DocuSign** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![DocuSign konfigurace](common/configuresection.png)

7. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu DocuSign** jako správce.

8. V horní pravé části stránky klikněte na profil **logo** a potom klikněte na **přejděte do správce**.
  
    ![Konfiguruje se jednotné přihlašování.][51]

9. Na stránce řešení domény, klikněte na **domén**

    ![Konfiguruje se jednotné přihlašování.][50]

10. V části **domén** klikněte na tlačítko **domény deklaraci identity**.

    ![Konfiguruje se jednotné přihlašování.][52]

11. Na **deklarace identity domény** dialogového okna v **název domény** textového pole zadejte doménu vaší společnosti a potom klikněte na tlačítko **deklarace identity**. Ujistěte se, že ověření domény a je ve stavu aktivní.

    ![Konfiguruje se jednotné přihlašování.][53]

12. Na stránce řešení domény, klikněte na tlačítko **zprostředkovatelé Identity**.
  
    ![Konfiguruje se jednotné přihlašování.][54]

13. V části **zprostředkovatelé Identity** klikněte na tlačítko **přidat zprostředkovatele IDENTITY**. 

    ![Konfiguruje se jednotné přihlašování.][55]

14. Na **nastavení zprostředkovatele Identity** stránce, proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][56]

    a. V **název** textového pole zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. V **textového pole Vystavitel zprostředkovatele Identity**, vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Vyberte **ověřovací přihlašovací požadavek**.

    f. Jako **žádost odeslat ověřovací**vyberte **příspěvek**.

    g. Jako **žádost odeslat odhlášení**vyberte **získat**.

    h. V **mapování vlastního atributu** části, klikněte na **přidat nové mapování**.

    ![Konfiguruje se jednotné přihlašování.][62]

    i. Zvolte pole, které chcete propojit s deklarací identity Azure AD. V tomto příkladu **emailaddress** deklarací identity je namapována na žádnou hodnotu **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jedná se o výchozí název deklarace identity z Azure AD pro deklarace identity e-mailu a pak klikněte na tlačítko **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][57]

    > [!NOTE]
    > Použít příslušné **identifikátor uživatele** mapovat uživatele ze služby Azure AD pro mapování uživatele DocuSign. Vyberte správné pole a zadejte příslušnou hodnotu na základě svého nastavení organizace.

    j. V **certifikáty zprostředkovatele Identity** klikněte na tlačítko **přidat certifikát**a pak nahrajte certifikát, který jste si stáhli z portálu Azure AD a klikněte na **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][58]

    k. V **zprostředkovatelé Identity** klikněte na tlačítko **akce**a potom klikněte na tlačítko **koncové body**.

    ![Konfiguruje se jednotné přihlašování.][59]

    l. V **zobrazit SAML 2.0 koncové body** části na **portál pro správu DocuSign**, proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][60]

    * Kopírovat **URL vystavitele poskytovatele služby**a vložte jej do **identifikátor** textového pole v **DocuSign domény a adresy URL** části na webu Azure portal.

    * Kopírovat **přihlašovací adresa URL služby zprostředkovatele**a vložte jej do **přihlašovací adresa URL** textového pole v **DocuSign domény a adresy URL** části na webu Azure portal.

    * Klikněte na tlačítko **zavřít**

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

### <a name="creating-a-docusign-test-user"></a>Vytvoření zkušebního uživatele DocuSign

Cílem této části je vytvořte uživatele Britta Simon v DocuSign. DocuSign podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k DocuSign, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon udělení přístupu k DocuSign používá Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **DocuSign**.

    ![Konfigurace jednotného přihlašování](./media/docusign-tutorial/tutorial_docusign_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici DocuSign na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci DocuSign.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
