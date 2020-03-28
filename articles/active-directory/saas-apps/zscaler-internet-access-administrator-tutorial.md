---
title: 'Kurz: Integrace služby Azure Active Directory se správcem internetového přístupu Zscaler | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a správcem internetového přístupu Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a149527c6e00972991bf0b18e6f7c599799a0c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161036"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Kurz: Integrace služby Azure Active Directory se správcem internetového přístupu Zscaler

V tomto kurzu se dozvíte, jak integrovat Správce přístupu k Internetu Zscaler s Azure Active Directory (Azure AD).
Integrace Správce internetového přístupu Zscaler s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Zscaler Správce přístupu k Internetu.
* Uživatelům můžete povolit automatické přihlášení ke správci internetového přístupu Zscaler (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD se správcem internetového přístupu Zscaler, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné správce internetového přístupu Zscaler

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Zscaler Internet Access Administrator podporuje iniciované služby při hlásači **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Přidání správce přístupu k Internetu Zscaler z galerie

Chcete-li nakonfigurovat integraci Správce internetového přístupu Zscaler do Azure AD, je třeba přidat Správce přístupu k Internetu Zscaler z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat správce přístupu k Internetu Zscaler z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Příkaz Zscaler Internet Access Administrator**, z panelu výsledků vyberte **Zscaler Internet Access Administrator** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![Správce přístupu k Internetu Zscaler v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí správce internetového přístupu Zscaler na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem ve Správci přístupu k Internetu Zscaler.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí správce internetového přístupu Zscaler, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení správce přístupu k Internetu Zscaler](#configure-zscaler-internet-access-administrator-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte zscaler Internet Access Administrator testovacího uživatele](#create-zscaler-internet-access-administrator-test-user)** – chcete-li mít protějšek Britta Simon v Zscaler Internet Access Administrator, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí správce internetového přístupu Zscaler, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Zscaler Internet Access Administrator** vyberte Jedno **přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na tlačítko **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Zscaler Internet Access Administrator Domain and URLs single sign-on information Zscaler Internet Access Administrator Domain and URLs single sign-on information Zscaler Internet Access Administrator Domain and URLs single sign-on information Zscaler Internet Access](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL podle vašeho požadavku:

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL podle vašeho požadavku:

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Aplikace Zscaler Internet Access Administrator očekává kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Hodnoty těchto atributů můžete spravovat v části **Atributy uživatele & deklarace identity** na stránce integrace aplikace. Na **stránce Nastavit jednotné přihlašování pomocí saml**klepněte na tlačítko **Upravit** a otevřete dialogové okno **Uživatelské atributy & deklarace identity.**

    ![Odkaz Atribut](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name (Název)  | Atribut zdroje  |
    | ---------| ------------ |
    | Role     | user.assignedroles |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Ze seznamu **atributů Source** selelct hodnotu atributu.

    c. Klikněte na **OK**.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Chcete-li vědět, jak nakonfigurovat roli ve službě Azure AD, klikněte [sem.](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. V části **Nastavit správce internetového přístupu Zscaler** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Konfigurace jednotného přihlašování správce internetového přístupu Zscaler

1. V jiném okně webového prohlížeče se přihlaste k uživatelskému rozhraní Zscaler Internet Access Admin.

2. Přejděte na **položku Správa správy > správce** a proveďte následující kroky a klepněte na tlačítko Uložit:

    ![Správa](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Správa")

    a. Zkontrolujte **povolit ověřování SAML**.

    b. Kliknutím na **Tlačítko Nahrát**nahrajete podpisový certifikát Azure SAML, který jste stáhli z portálu Azure, do **veřejného certifikátu SSL**.

    c. Volitelně pro další zabezpečení přidejte podrobnosti **vystavittele** k ověření vystavittele odpovědi SAML.

3. V uživatelském rozhraní pro správu proveďte následující kroky:

    ![Správa](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Najeďte na nabídku **Aktivace** v levém dolním rohu.

    b. Klepněte na **tlačítko Aktivovat**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Zscaler Internet Access Administrator.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Zscaler Internet Access Administrator**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **položku Zscaler Internet Access Administrator**.

    ![Odkaz Správce přístupu k Internetu Zscaler v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Vytvořit testovacího uživatele správce internetu Zscaler

Cílem této části je vytvořit uživatele s názvem Britta Simon v Zscaler Správce přístupu k Internetu. Zscaler Internet Access nepodporuje just-in-time zřizování pro správce semaforu. Je nutné ručně vytvořit účet správce.
Postup vytvoření účtu správce naleznete v dokumentaci k zscaleru:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Zscaler Internet Access Administrator na přístupovém panelu, měli byste být automaticky přihlášeni k uživatelskému rozhraní Zscaler Internet Access Admin, pro které nastavíte přihlašování. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
