---
title: 'Kurz: Integrace služby Azure Active Directory se službou Symantec Web Security Service (WSS) | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi službou Azure Active Directory a službou Symantec Web Security Service (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159941"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Integrace služby Azure Active Directory se službou Symantec Web Security Service (WSS)

V tomto kurzu se dozvíte, jak integrovat účet Služby Symantec Web Security Service (WSS) s účtem Azure Active Directory (Azure AD), aby wss mohl ověřovat koncového uživatele zřízeného ve službě Azure AD pomocí ověřování SAML a vynucovat uživatele nebo pravidla zásad na úrovni skupiny.

Integrace služby Symantec Web Security Service (WSS) s Azure AD vám poskytuje následující výhody:

- Spravujte všechny koncové uživatele a skupiny používané vaším účtem WSS z portálu Azure AD.

- Umožněte koncovým uživatelům, aby se ověřili ve službě WSS pomocí svých přihlašovacích údajů služby Azure AD.

- Povolte vynucení pravidel zásad na úrovni uživatelů a skupin definovaných ve vašem účtu WSS.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci azure ad se službou Symantec Web Security Service (WSS), potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením služby Symantec Web Security Service (WSS)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Symantec Web Security Service (WSS) podporuje iniciované služby SSO iniciované **iniciací IDP**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Přidání služby Symantec Web Security Service (WSS) z galerie

Chcete-li nakonfigurovat integraci služby Symantec Web Security Service (WSS) do služby Azure AD, je třeba přidat službu WSS (Symantec Web Security Service) z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat službu WSS (Symantec Web Security Service) z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **službu WSS (Symantec Web Security Service),** z panelu výsledků vyberte možnost **Symantec Web Security Service (WSS)** a klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Symantec Web Security Service (WSS) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování služby Azure AD pomocí služby WSS (Symantec Web Security Service) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě Symantec Web Security Service (WSS).

Chcete-li konfigurovat a testovat jednotné přihlašování azure ad pomocí služby Symantec Web Security Service (WSS), je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **Konfigurace jednotného přihlašování služby Symantec Web Security Service (WSS)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele služby Symantec Web Security Service (WSS),](#create-symantec-web-security-service-wss-test-user)** chcete-li mít protějšek Britty Simonové ve službě WSS (Symantec Web Security Service), která je propojena s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování služby Azure AD pomocí služby WSS (Symantec Web Security Service), proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Symantec Web Security Service (WSS)** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V dialogovém okně **Základní konfigurace saml** proveďte následující kroky:

    ![Informace o doméně a adresách URL domény a adresy URL služby Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL:`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL:`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Obraťte se [na tým podpory klienta Symantec Web Security Service (WSS),](https://www.symantec.com/contact-us) který se může nařídit, a protože hodnoty **adresy URL identifikátorů** a **odpovědí** z nějakého důvodu nefungují. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurace jednotného přihlašování služby Symantec Web Security Service (WSS)

Chcete-li konfigurovat jednotné přihlašování na straně služby WSS (Symantec Web Security Service), přečtěte si dokumentaci k webu WSS online. Stažený **xml metadat federace** bude nutné importovat na portál WSS. Pokud potřebujete pomoc s konfigurací na portálu WSS, obraťte se na [tým podpory služby Symantec Web Security Service (WSS).](https://www.symantec.com/contact-us)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Symantec Web Security Service (WSS).

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Symantec Web Security Service (WSS).**

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **službu Symantec Web Security Service (WSS).**

    ![Odkaz WSS (Symantec Web Security Service) v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-symantec-web-security-service-wss-test-user"></a>Vytvoření testovacího uživatele služby Symantec Web Security Service (WSS)

V této části vytvoříte uživatele s názvem Britta Simon v symantec Web Security Service (WSS). Odpovídající koncové uživatelské jméno lze ručně vytvořit na portálu WSS nebo můžete počkat na uživatele nebo skupiny zřízené ve službě Azure AD, které mají být synchronizovány na portálu WSS po několika minutách (~15 minut). Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení. Veřejná IP adresa počítače koncového uživatele, která bude použita k procházení webů, musí být také zřízena na portálu WSS (Symantec Web Security Service).

> [!NOTE]
> Chcete-li získat veřejnou ipadresu vašeho počítače, klikněte [sem.](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete funkci jednotného přihlašování, když jste nakonfigurovali svůj účet WSS tak, aby používal vaše Azure AD pro ověřování SAML.

Po nakonfigurování webového prohlížeče na přenos proxy na WSS, při otevření webového prohlížeče a pokusu o procházení webu pak budete přesměrováni na přihlašovací stránku Azure. Zadejte přihlašovací údaje koncového uživatele testu, který byl zřízen ve službě Azure AD (to znamená BrittaSimon) a přidružené heslo. Po ověření budete moci přejít na web, který jste zvolili. Pokud vytvoříte pravidlo zásad na straně WSS blokovat BrittaSimon z procházení na konkrétní web, pak byste měli vidět wss blok stránky při pokusu o procházení tohoto webu jako uživatel BrittaSimon.

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

