---
title: 'Kurz: Integrace služby Azure Active Directory se službou Adobe Sign | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154020"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Integrace služby Azure Active Directory se službou Adobe Sign

V tomto kurzu se dozvíte, jak integrovat Adobe Sign s Azure Active Directory (Azure AD).
Integrace služby Adobe Sign s Azure AD vám přináší následující výhody:

* Ve službě Azure AD můžete řídit, kdo má přístup ke službě Adobe Sign.
* Uživatelům můžete povolit automatické přihlášení ke službě Adobe Sign (jednotné přihlášení) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí služby Adobe Sign, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením služby Adobe Sign

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Sign podporuje služby SSO iniciované **sp**

## <a name="adding-adobe-sign-from-the-gallery"></a>Přidání služby Adobe Sign z galerie

Chcete-li nakonfigurovat integraci služby Adobe Sign do služby Azure AD, musíte přidat službu Adobe Sign z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat službu Adobe Sign z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Sign**, z panelu výsledků vyberte **Adobe Sign** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Adobe Sign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby Adobe Sign na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve službě Adobe Sign.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování azure ad pomocí služby Adobe Sign, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování služby Adobe Sign](#configure-adobe-sign-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele služby Adobe Sign](#create-adobe-sign-test-user)** – chcete-li mít ve službě Adobe Sign protějšek Britty Simonové, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování služby Azure AD pomocí služby Adobe Sign, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Adobe Sign** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Doméně adobe sign a adres URL informací o jednotném přihlášení](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.echosign.com/`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.echosign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li tyto hodnoty získat, obraťte se na [tým podpory klienta Adobe Sign.](https://helpx.adobe.com/in/contact/support.html) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit službu Adobe Sign** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-adobe-sign-single-sign-on"></a>Konfigurace jednotného přihlášení služby Adobe Sign

1. Před konfigurací se obraťte na [tým podpory klienta Služby Adobe Sign a](https://helpx.adobe.com/in/contact/support.html) přidejte svou doménu do seznamu povolených položek Adobe Sign. Zde je návod, jak přidat doménu:

    a. [Tým podpory klienta Adobe Sign](https://helpx.adobe.com/in/contact/support.html) vám pošle náhodně generovaný token. Pro vaši doménu bude token vypadat jako následující: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikujte ověřovací token v textovém záznamu DNS a upozorněte [tým podpory klienta Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > To může trvat několik dní nebo déle. Všimněte si, že zpoždění šíření DNS znamená, že hodnota publikovaná ve službě DNS nemusí být viditelná po dobu jedné hodiny nebo déle. Správce IT by měl mít znalosti o tom, jak publikovat tento token v textovém záznamu DNS.
    
    c. Když upozorníte [tým podpory klienta Adobe Sign](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory, po publikování tokenu ověří doménu a přidá ji do vašeho účtu.
    
    d. Obecně platí, že zde je postup publikování tokenu na záznamu DNS:

    * Přihlášení k účtu domény
    * Vyhledejte stránku pro aktualizaci záznamu DNS. Tato stránka se může jmenovat Správa DNS, Správa názvového serveru nebo Upřesňující nastavení.
    * Vyhledejte záznamy TXT pro vaši doménu.
    * Přidejte záznam TXT s plnou hodnotou tokenu dodanou společností Adobe.
    * Uložte provedené změny.

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Adobe Sign jako správce.

1. V nabídce SAML vyberte **Nastavení** > účtu**NASTAVENÍ SAML**.
   
    ![Snímek obrazovky se stránkou Nastavení adobe sign saml](./media/adobe-echosign-tutorial/ic789520.png "Účet")

1. V části **Nastavení SAML** proveďte následující kroky:
  
   ![Snímek obrazovky s nastavením SAML](./media/adobe-echosign-tutorial/ic789521.png "Nastavení SAML")
   
   ![Snímek obrazovky s nastavením SAML](./media/adobe-echosign-tutorial/ic789522.png "Nastavení SAML")

   a. V **části Režim SAML**vyberte **možnost SAML Mandatory**.
   
   b. Vyberte **Povolit správcům účtů Echosign, aby se přihlašovali pomocí svých přihlašovacích údajů echosignu**.
   
   c. V části **User Creation**vyberte Automaticky přidávat **uživatele ověřené pomocí saml**.

   d. Vložte **identifikátor azure reklamy**, který jste zkopírovali z portálu Azure do textového pole **Idp Entity ID.**
    
   e. Vložte **přihlašovací adresu URL**, kterou jste zkopírovali z portálu Azure do textového pole Adresa URL přihlášení **idp.**
   
   f. Vložte **adresu URL odhlášení**, kterou jste zkopírovali z portálu Azure do textového pole **URL odhlášení z Idpu.**

   g. Otevřete stažený soubor **certifikátu (Base64)** v poznámkovém bloku. Zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát u svého zástupce.**

   h. Vyberte **Uložit změny**.

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

V této části umožníte Brittě Simonové používat jednotné přihlašování Azure udělením přístupu ke službě Adobe Sign.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Adobe **Sign**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Adobe Sign**.

    ![Odkaz Adobe Sign v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-adobe-sign-test-user"></a>Vytvoření testovacího uživatele služby Adobe Sign

Aby se uživatelé Azure AD mohli přihlásit ke službě Adobe Sign, musí být zřízeni do služby Adobe Sign. Jedná se o ruční úkol.

>[!NOTE]
>Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů služby Adobe Sign nebo rozhraní API poskytovaná službou Adobe Sign. 

1. Přihlaste se k webu společnosti **Adobe Sign** jako správce.

2. V nabídce nahoře vyberte **Účet**. Potom v levém podokně vyberte **možnost Uživatelé & skupiny** > **Vytvořit nového uživatele**.
   
    ![Snímek obrazovky s webem společnosti Adobe Sign se zvýrazněným účtem, uživateli &skupinami a vytvořením nového uživatele](./media/adobe-echosign-tutorial/ic789524.png "Účet")
   
3. V části **Vytvořit nového uživatele** proveďte následující kroky:
   
    ![Snímek obrazovky s oddílem Vytvořit nového uživatele](./media/adobe-echosign-tutorial/ic789525.png "Vytvořit uživatele")
   
    a. Zadejte **e-mailovou adresu**, **jméno**a **příjmení** platného účtu Azure AD, který chcete zřídit do souvisejících textových polí.
   
    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz pro potvrzení účtu, než se stane aktivním. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Sign na přístupovém panelu byste měli být automaticky přihlášeni ke službě Adobe Sign, pro kterou nastavíte službu přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

