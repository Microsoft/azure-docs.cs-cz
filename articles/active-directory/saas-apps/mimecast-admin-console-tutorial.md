---
title: 'Kurz: Integrace služby Azure Active Directory s Konzolou správce Mimecastu | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Konzolou pro správu Mimecastu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160665"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Kurz: Integrace Služby Azure Active Directory s Konzolou správce Mimecast

V tomto kurzu se dozvíte, jak integrovat Konzolu pro správu Mimecast u Azure Active Directory (Azure AD).
Integrace Konzoly pro správu Mimecastu s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Konzoli pro správu Mimecastu.
* Uživatelům můžete povolit automatické přihlášení ke konzoli Mimecast Admin Console (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Konzoly pro správu Mimecastu, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením v Konzole Mimecast

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Mimecast Admin Console podporuje **sp** inicioval přisuzování služeb Přijmuzování

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Přidání Konzoly pro správu Mimecastu z galerie

Pokud chcete nakonfigurovat integraci Konzoly pro správu Mimecastu do Azure AD, musíte přidat konzolu Správce Mimecastu z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat konzolu Mimecast Admin Console z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Konzolu pro správu Mimecastu**, z panelu výsledků vyberte **Konzolu správce Mimecast** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Konzola správce Mimecast u v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí konzoly Mimecast Admin Console na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Konzole pro správu Mimecastu.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Konzoly pro správu Mimecastu, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení konzoly Mimecast Admin Console](#configure-mimecast-admin-console-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Správce Mimecastu](#create-mimecast-admin-console-test-user)** – chcete-li mít protějšek Britty Simonové v Konzole pro správu Mimecastu, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Konzoly pro správu Mimecastu, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte na stránce integrace aplikací **Mimecast Admin Console** možnost Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení do sudodové konzoly Mimecast a adresy URL](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Adresa URL znaku na adrese URL je specifická pro oblast.

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Konzolu pro správu Mimecast** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Konfigurace jednotného přihlášení konzoly Mimecast Admin Console

1. V jiném okně webového prohlížeče se přihlaste do konzole Mimecast Admin Console jako správce.

2. Přejděte do **aplikace Služby \> **.

    ![Služby](./media/mimecast-admin-console-tutorial/ic794998.png "Služby")

3. Klepněte na **položku Profily ověřování**.

    ![Profily ověřování](./media/mimecast-admin-console-tutorial/ic794999.png "Profily ověřování")
    
4. Klepněte na **položku Nový profil ověřování**.

    ![Nové profily ověřování](./media/mimecast-admin-console-tutorial/ic795000.png "Nové profily ověřování")

5. V části **Profil ověřování** proveďte následující kroky:

    ![Profil ověřování](./media/mimecast-admin-console-tutorial/ic795015.png "Profil ověřování")
    
    a. Do textového pole **Popis** zadejte název konfigurace.
    
    b. Vyberte **Vynutit ověřování SAML pro Konzolu pro správu Mimecastu**.
    
    c. Jako **zprostředkovatel**vyberte **službu Azure Active Directory**.
    
    d. Vložte **identifikátor azure reklamy**, který jste zkopírovali z portálu Azure do textového pole **URL vystavitistu.**
    
    e. Vložte **přihlašovací adresu URL**, kterou jste zkopírovali z portálu Azure do textového pole Přihlašovací adresa **URL.**

    f. Vložte **přihlašovací adresu URL**, kterou jste zkopírovali z portálu Azure do textového pole Adresa URL **odhlášení.**
    
    >[!NOTE]
    >Hodnota přihlašovací adresy URL a hodnota adresy URL odhlášení jsou pro Konzolu správce Mimecast stejné.
    
    g. Otevřete svůj certifikát base-64 stažený z portálu Azure*--* v poznámkovém bloku,*--* odeberte první řádek (" ") a poslední řádek ("), zkopírujte zbývající obsah do schránky a vložte jej do textového pole **Certifikát u poskytovatele identity (metadata).**
    
    h. Vyberte **povolit jednotné přihlašování**.
    
    i. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Mimecast Admin Console.

1. Na webu Azure Portal vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Konzolu pro správu Mimecastu**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **Konzolu pro správu Mimecast**.

    ![Odkaz Správce Mimecastu v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-mimecast-admin-console-test-user"></a>Vytvořit uživatele testu správce Mimecastu

Aby se uživatelé Azure AD mohli přihlásit do Konzoly pro správu Mimecastu, musí být zřízeni do Konzoly pro správu Mimecastu. V případě Mimecast Admin Console zřizování je ruční úloha.

* Před vytvořením uživatelů je třeba zaregistrovat doménu.

**Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se ke **správci Konzoly Správce Mimecastu.**

2. Přejděte na **Adresáře \> Interní**.
   
    ![Adresáře](./media/mimecast-admin-console-tutorial/ic795003.png "Adresáře")

3. Klepněte na **tlačítko Registrovat novou doménu**.
   
    ![Zaregistrovat novou doménu](./media/mimecast-admin-console-tutorial/ic795004.png "Zaregistrovat novou doménu")

4. Po vytvoření nové domény klepněte na tlačítko **Nová adresa**.
   
    ![Nová adresa](./media/mimecast-admin-console-tutorial/ic795005.png "Nová adresa")

5. V dialogovém okně nová adresa proveďte následující kroky:
   
    ![Uložit](./media/mimecast-admin-console-tutorial/ic795006.png "Uložit")
   
    a. Zadejte **atributy E-mailová adresa**, **Globální název**, **Heslo**a **Potvrdit heslo** platného účtu Azure AD, který chcete zřídit, do souvisejících textových polí.

    b. Klikněte na **Uložit**.

>[!NOTE]
>Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Konzoly pro správu Mimecast nebo rozhraní API poskytovaná konzolou Mimecast Admin Console. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Mimecast Admin Console na přístupovém panelu, měli byste být automaticky přihlášeni do Konzoly pro správu Mimecast, pro které nastavíte přiřazování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

