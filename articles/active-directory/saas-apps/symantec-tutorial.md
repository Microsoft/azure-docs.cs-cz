---
title: 'Kurz: Integrace s Azure Active Directory s Symantec webové zabezpečení služby (WSS) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Symantec webové zabezpečení služby (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: fb2247b15cf32326f141e1d0973bdf9145d92534
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065253"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Integrace s Azure Active Directory s Symantec webové zabezpečení služby (WSS)

V tomto kurzu se dozvíte, jak integrovat účtu Symantec webové zabezpečení služby (WSS) pomocí svého účtu Azure Active Directory (Azure AD), tak, aby WSS může ověřovat koncový uživatel zřízenou ve službě Azure AD pomocí ověřování SAML a vynutit uživatele nebo pravidla úrovně zásad skupiny.

Integrace Symantec webové zabezpečení služby (WSS) s Azure AD poskytuje následující výhody:

- Spravujte všechny koncoví uživatelé a skupiny používané vaším účtem WSS z portálu Azure AD.

- Povolit koncovým uživatelům sami ověření v WSS pomocí svých přihlašovacích údajů Azure AD.

- Povolit vynucení uživatele a skupiny zásadou na úrovni pravidel definovaných ve vašem účtu služby WSS.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Symantec webové zabezpečení služby (WSS), potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Symantec webové zabezpečení služby (WSS) jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Symantec webové zabezpečení služby (WSS) podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Přidání Symantec webové zabezpečení služby (WSS) z Galerie

Konfigurace integrace nástroje Symantec webové zabezpečení služby (WSS) do služby Azure AD, budete muset přidat Symantec webové zabezpečení služby (WSS) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Symantec webové zabezpečení služby (WSS) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Symantec webové zabezpečení služby (WSS)** vyberte **Symantec webové zabezpečení služby (WSS)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Symantec webové zabezpečení služby (WSS) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS) na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů ve společnosti Symantec webové zabezpečení služby (WSS).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace webové zabezpečení služby (WSS) Symantec Single Sign-On](#configure-symantec-web-security-service-(wss)-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Symantec webové zabezpečení služby (WSS)](#create-symantec-web-security-service-wss-test-user)**  – Pokud chcete mít protějšek Britta Simon v Symantec webové zabezpečení služby (služby WSS), který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Symantec webové zabezpečení služby (WSS)** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** dialogového okna, proveďte následující kroky:

    ![Symantec webové zabezpečení služby (WSS) domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kontakt [tým podpory Symantec webové zabezpečení služby (WSS) klienta](https://www.symantec.com/contact-us) f hodnoty **identifikátor** a **adresy URL odpovědi** nefungují z nějakého důvodu... Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurace webové Symantec zabezpečení služby (WSS) jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování na straně Symantec webové zabezpečení služby (WSS), najdete v dokumentaci online WSS. Na stažený **kód XML metadat federace** muset importovat do portálu WSS. Obraťte se [Symantec webové zabezpečení služby (WSS) tým podpory](https://www.symantec.com/contact-us) Pokud potřebujete pomoc s konfigurací na portálu služby WSS.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Symantec webové zabezpečení služby (WSS).

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Symantec webové zabezpečení služby (WSS)**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Symantec webové zabezpečení služby (WSS)**.

    ![Odkaz Symantec webové zabezpečení služby (WSS) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Vytvořit testovacího uživatele Symantec webové zabezpečení služby (WSS)

V této části vytvoříte uživateli Britta Simon v Symantec webové zabezpečení služby (WSS). Odpovídající koncové uživatelské jméno je ručně vytvořit na portálu služby WSS nebo můžete počkat, uživatele nebo skupiny zřízenou ve službě Azure AD a budou synchronizovat na portálu služby WSS za pár minut (přibližně 15 minut). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. Veřejnou IP adresu počítače koncového uživatele, který se použije k procházení websites je také potřeba ho zřídit na portálu společnosti Symantec webové zabezpečení služby (WSS).

> [!NOTE]
> Po klepnutí na [tady](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) získat váš počítač je veřejná IP adresa.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části budete testovat funkce přihlašování – teď, když jste nakonfigurovali WSS účet pro použití služby Azure AD pro ověřování SAML.

Po nakonfigurování ve webovém prohlížeči proxy provoz na WSS, když otevřete webový prohlížeč a zkuste přejít na web potom budete přesměrováni na stránku Azure přihlašování. Zadání přihlašovacích údajů koncového uživatele testu, které se zřizují ve službě Azure AD (BrittaSimon) a přidružené heslo. Po ověření, budete moct přejít na web, který jste zvolili. Můžete vytvořit pravidlo zásad na straně služby WSS znemožní BrittaSimon procházení určité lokalitě, pak by měl naleznete na stránce bloku WSS, při pokusu o procházení k dané lokalitě jako uživatel BrittaSimon.

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

