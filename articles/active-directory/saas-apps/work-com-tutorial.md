---
title: 'Kurz: Integrace služby Azure Active Directory s Work.com | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087081"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Kurz: Integrace Azure Active Directory s Work.com

V tomto kurzu se dozvíte, jak integrovat Work.com s Azure Active Directory (Azure AD).
Integrace Work.com s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Work.com.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k Work.com (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Work.com, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Work.com předplatné s povolenou přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Work.com podporuje **sp** inicioval sso

## <a name="adding-workcom-from-the-gallery"></a>Přidání Work.com z galerie

Chcete-li nakonfigurovat integraci Work.com do Azure AD, je potřeba přidat Work.com z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Work.com z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Work.com**, vyberte **Work.com** z panelu výsledků a klepnutím na **tlačítko Přidat** přidejte aplikaci.

    ![Work.com v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Work.com na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Work.com.

Chcete-li konfigurovat a testovat jednotné přihlašování azure ad s Work.com, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace Work.com jednotného přihlašování](#configure-workcom-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte Work.com testovacího uživatele](#create-workcom-test-user)** – chcete-li mít protějšek Britta Simon v Work.com, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

>[!NOTE]
>Chcete-li konfigurovat jednotné přihlašování, musíte ještě nastavit vlastní Work.com název domény. Musíte definovat alespoň název domény, otestovat název domény a nasadit jej do celé organizace.

Chcete-li nakonfigurovat jednotné přihlašování azure a disponu, pomocí Work.com, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace **Work.com** aplikací vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Work.com informace o jedné registraci domény a adresy URL](common/sp-signonurl.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Hodnota není skutečná. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Obraťte [se Work.com tým podpory klienta](https://help.salesforce.com/articleView?id=000159855&type=3) získat hodnotu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Work.com** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-workcom-single-sign-on"></a>Konfigurace Work.com jednotného přihlašování

1. Přihlaste se ke svému Work.com tenantovi jako správce.

2. Přejděte na **instalační program**.
   
    ![Nastavení](./media/work-com-tutorial/ic794108.png "Nastavení")

3. V levém navigačním podokně klikněte v části **Správa** na **položku Správa domény** a rozbalte související oddíl a potom kliknutím na **Moje doména** otevřete stránku **Moje doména.** 
   
    ![Moje doména](./media/work-com-tutorial/ic767825.png "Moje doména")

4. Chcete-li ověřit, zda byla vaše doména správně nastavena, zkontrolujte, zda je v kroku**4 nasazeném uživatelům,** a zkontrolujte**nastavení domény**.
   
    ![Doména nasazená pro uživatele](./media/work-com-tutorial/ic784377.png "Doména nasazená pro uživatele")

5. Přihlaste se ke svému Work.com tenantovi.

6. Přejděte na **instalační program**.
    
    ![Nastavení](./media/work-com-tutorial/ic794108.png "Nastavení")

7. Rozbalte nabídku **Ovládací prvky zabezpečení** a klepněte na **položku Nastavení jednotného přihlášení**.
    
    ![Nastavení jednotného přihlášení](./media/work-com-tutorial/ic794113.png "Nastavení jednotného přihlášení")

8. Na stránce dialogového okna **Nastavení jednotného přihlášení** proveďte následující kroky:
    
    ![SAML povoleno](./media/work-com-tutorial/ic781026.png "SAML povoleno")
    
    a. Vyberte **možnost SAML Povolená**.
    
    b. Klepněte na tlačítko **Nový**.

9. V části **Nastavení jednotného přihlašování SAML** proveďte následující kroky:
    
    ![Saml nastavení jednotného přihlášení](./media/work-com-tutorial/ic794114.png "Saml nastavení jednotného přihlášení")
    
    a. Do textového pole **Název** zadejte název konfigurace.  
       
    > [!NOTE]
    > Zadání matné hodnoty **pro název** automaticky naplní textové pole **Název rozhraní API.**
    
    b. V **textovém** poli Vystavite na vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali z webu Azure Portal.
    
    c. Pokud chcete nahrát stažený certifikát z webu Azure Portal, klikněte na **Procházet**.
    
    d. Do textového pole **Id entity** zadejte . `https://salesforce-work.com`
    
    e. Jako **typ identity SAML**vyberte **assertion obsahuje ID federace z objektu User**.
    
    f. Jako **umístění identity SAML**je vyberte **Identita v elementu NameIdentfier příkazu Subject**.
    
    g. V textovém poli **Přihlašovací adresa URL zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.

    h. V textovém poli **URL odhlášení zprostředkovatele identity** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.
    
    i. Jako **závazek požadavku iniciovaného poskytovatelem služeb**vyberte **příkaz HTTP Post**.
    
    j. Klikněte na **Uložit**.

10. Na klasickém portálu Work.com v levém navigačním podokně **rozbalte** související oddíl kliknutím na Správa domény a potom kliknutím na **Moje doména** otevřete stránku **Moje doména.** 
    
    ![Moje doména](./media/work-com-tutorial/ic794115.png "Moje doména")

11. Na stránce **Moje doména** klikněte v části **Branding přihlašovací stránky** na **Upravit**.
    
    ![Přihlašovací stránka značky](./media/work-com-tutorial/ic767826.png "Přihlašovací stránka značky")

12. Na stránce **Branding přihlašovací stránky** se v části Ověřovací **služba** zobrazí název **nastavení přihlašování saml.** Vyberte ji a klepněte na tlačítko **Uložit**.
    
    ![Přihlašovací stránka značky](./media/work-com-tutorial/ic784366.png "Přihlašovací stránka značky")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** `brittasimon@yourcompanydomain.extension`jméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Work.com.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak **vyberte Work.com**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Work.com**.

    ![Odkaz Work.com v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-workcom-test-user"></a>Vytvořit Work.com testovacího uživatele

Aby se uživatelé služby Azure Active Directory mohli přihlásit, musí být zřízeni, aby Work.com. V případě Work.com zřizování je ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Chcete-li konfigurovat zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k webu společnosti Work.com jako správce.

2. Přejděte na **instalační program**.
   
    ![Nastavení](./media/work-com-tutorial/IC794108.png "Nastavení")

3. Přejděte na **spravovat uživatele \> uživatelů**.
   
    ![Správa uživatelů](./media/work-com-tutorial/IC784369.png "Správa uživatelů")

4. Klepněte na **položku Nový uživatel**.
   
    ![Všichni uživatelé](./media/work-com-tutorial/IC794117.png "Všichni uživatelé")

5. V části Úpravy uživatelů proveďte následující kroky v atributech platného účtu Azure AD, který chcete zřídit do souvisejících textových polí:
   
    ![Úpravy uživatele](./media/work-com-tutorial/ic794118.png "Úpravy uživatele")
   
    a. Do textového pole **Křestní jméno** zadejte **křestní jméno** uživatele **Britta**.
    
    b. Do textového pole **Příjmení** zadejte **příjmení** uživatele **Simona**.
    
    c. Do textového pole **Alias** zadejte **jméno** uživatele **BrittaS**.
    
    d. Do textového pole **E-mail** zadejte **e-mailovou adresu** uživatele Brittasimon@contoso.com.
    
    e. Do textového pole **Uživatelské jméno** zadejte Brittasimon@contoso.comuživatelské jméno uživatele, jako se mi líbí .
    
    f. Do textového pole **Nick Název** zadejte **přezdívku** uživatele **Simona**.
    
    g. Vyberte **roli**, **uživatelskou licenci**a **profil**.
    
    h. Klikněte na **Uložit**.  
      
    > [!NOTE]
    > Držitel účtu Azure AD obdrží e-mail včetně odkazu pro potvrzení účtu před tím, než se stane aktivním.
    > 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Work.com na přístupovém panelu, můžete by měl být automaticky přihlášeni k Work.com, pro které nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

