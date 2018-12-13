---
title: 'Kurz: Integrace Azure Active Directory s nekonečnou Campus | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a školních nekonečné.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 9f4adbacf2749e8c8ff2da8f331a007e8dcaaea3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099949"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Kurz: Integrace Azure Active Directory s nekonečnou Campus

V tomto kurzu se dozvíte, jak integrovat nekonečné Campus s Azure Active Directory (Azure AD).

Nekonečné Campus integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k nekonečné Campus.
- Uživatele, aby automaticky získat přihlášení k nekonečné Campus (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s nekonečnou Campus, potřebujete následující položky:

- Předplatné Azure AD
- Nekonečnou Campus jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- Minimálně musíte být správce Azure Active Directory a dokončete tak konfiguraci.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání nekonečné Campus z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-infinite-campus-from-the-gallery"></a>Přidání nekonečné Campus z Galerie

Ke konfiguraci integrace nekonečné Campus do služby Azure AD, budete muset přidat nekonečné Campus z Galerie na váš seznam spravovaných aplikací SaaS.

**Nekonečné Campus přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **nekonečné Campus**vyberte **nekonečné Campus** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Nekonečné Campus v seznamu výsledků](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí nekonečné Campus podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v nekonečné Campus je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v nekonečné Campus potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nekonečnou areálu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele nekonečné Campus](#creating-a-infinite-campus-test-user)**  – Pokud chcete mít protějšek Britta Simon v nekonečné areálu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci nekonečné Campus.

**Ke konfiguraci Azure AD jednotné přihlašování s nekonečnou areálu, proveďte následující kroky:**

1. Na webu Azure Portal na **nekonečné Campus** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

5. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb** (přejděte ke kroku **11.c**), proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

        ![image](common/b9_saml.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/b9(1)_saml.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části textového pole, jak je znázorněno níže :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následující vzorek (doména bude lišit podle hostování modelu): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Můžete získat **soubor metadat poskytovatele služeb** na stránce nekonečné Campus jednotného přihlašování služby poskytovatele konfigurace, který je vysvětlen později v tomto kurzu. Pokud začínáte s novou konfigurací SAML poskytovatele služeb v nekonečné Campus, přejděte k **krok 11** k dokončení exportu souboru metadat poskytovatele služeb.

6. Pokud nemáte **soubor metadat poskytovatele služeb**, postupujte takto (Všimněte si, že doménu se bude lišit podle hostování modelu):

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nekonečné Campus domény a adresy URL jednotného přihlašování – informace](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko kopírování **ikonu** kopírování **adresa Url metadat federace aplikace**  a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Na **nastavení nekonečné Campus** oddílu, použijte následující hodnoty k ověření při odesílání nebo využívají soubor nebo adresa URL Azure metadat.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Nekonečné Campus konfigurace](common/configuresection.png)

8. V jiné okno webového prohlížeče, přihlaste se k nekonečné Campus jako správce zabezpečení.

9. V levé nabídce klikněte na tlačítko **Správa systému**.

    ![Správce](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Přejděte do **zabezpečení uživatele** > **SAML správu** > **konfigurace poskytovatele služby jednotného přihlašování**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Na **konfigurace poskytovatele služby jednotného přihlašování** stránce, proveďte následující kroky:

    ![Jednotné přihlašování](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Vyberte **povolit SAML jednotného přihlašování**.
    
    b. Na **vyberte možnost načíst data serveru zprostředkovatele Identity (IDP)** vyberte **adresa URL metadat**, vložte **adresa Url federačních metadat aplikace** v poli a pak Klikněte na tlačítko **synchronizace**.

    c. Klikněte na **Metadata poskytovatele služby** odkaz můžete uložit **soubor metadat poskytovatele služeb** v počítači a nahrajte ho v **základní konfiguraci SAML** části auto naplnění **identifikátor** a **adresy URL odpovědi** hodnoty na webu Azure Portal (najdete v kroku 4 pro nahrávání a automatické naplnění hodnot nebo kroku 5 pro ruční zadání).

    d. Po kliknutí na tlačítko **synchronizace** hodnoty získat automaticky dosadí **konfigurace poskytovatele služby jednotného přihlašování** stránky.

    e. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvořit _jeden_ testovacího uživatele na webu Azure Portal volá Britta Simon.

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

### <a name="creating-a-infinite-campus-test-user"></a>Vytvoření zkušebního uživatele nekonečné Campus

Nekonečné Campus má demografické údaje na architekturu. Obraťte se prosím na [tým podpory nekonečné Campus](mailto:sales@infinitecampus.com) přidat uživatele na platformě nekonečné Campus.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k nekonečné Campus.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **nekonečné Campus**.

    ![Konfigurace jednotného přihlašování](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici nekonečné Campus na přístupovém panelu, vám by měl získat automaticky přihlášení k nekonečné Campus aplikace. Pokud se přihlašujete do aplikace nekonečné Camnpus ve stejném prohlížeči, je Správa služby Azure AD, ujistěte se, že jste se přihlásili do služby Azure AD jako testovací uživatel. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

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
