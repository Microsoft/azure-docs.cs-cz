---
title: 'Kurz: Integrace služby Azure Active Directory s Pictureparkem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Pictureparkem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177013"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Kurz: Integrace Služby Azure Active Directory s Pictureparkem

V tomto kurzu se dozvíte, jak integrovat Picturepark s Azure Active Directory (Azure AD).
Integrace Pictureparku s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Picturepark.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Pictureparku (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD pomocí Pictureparku, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením Picturepark

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Picturepark podporuje **sp** inicioval SSO

## <a name="adding-picturepark-from-the-gallery"></a>Přidání Pictureparku z galerie

Chcete-li nakonfigurovat integraci Picturepark do Azure AD, musíte přidat Picturepark z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Picturepark z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Picturepark**, z panelu výsledků vyberte **Picturepark** a pak kliknutím na **tlačítko Přidat** přidejte aplikaci.

     ![Picturepark v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Pictureparku na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Pictureparku.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Pictureparku, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení Picturepark](#configure-picturepark-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte test Picturepark uživatele](#create-picturepark-test-user)** – mít protějšek Britta Simon v Picturepark, který je propojený s reprezentaci Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí Pictureparku, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Picturepark** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Picturepark Doména a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://<companyname>.picturepark.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta Picturepark.](https://picturepark.com/company/picturepark-customer-support) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. V části **Podpisový certifikát SAML** kliknutím na **tlačítko Upravit** otevřete dialogové okno **Podpisový certifikát SAML.**

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **Podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte jej do počítače.

    ![Kopírovat kryptografický otisk, hodnota](common/copy-thumbprint.png)

7. V části **Nastavit Picturepark** zkopírujte příslušnou adresu URL podle vašeho požadavku. Pro **přihlašovací adresu URL**použijte hodnotu s následujícím vzorem:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je id klienta předplatného Azure AD.

    ![Kopírování konfiguračních adres URL](./media/picturepark-tutorial/configurls.png)

    a. Identifikátor azure reklamy

    b. Adresa URL odhlášení

### <a name="configure-picturepark-single-sign-on"></a>Konfigurace jednotného přihlášení Picturepark

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Picturepark jako správce.

2. Na panelu nástrojů v horní části klepněte na **položku Nástroje pro správu**a potom klepněte na příkaz **Konzola správy**.
   
    ![Konzola pro správu](./media/picturepark-tutorial/ic795062.png "Konzola pro správu")

3. Klepněte na tlačítko **Ověřování**a potom klepněte na **položku Zprostředkovatelé identity**.
   
    ![Ověřování](./media/picturepark-tutorial/ic795063.png "Ověřování")

4. V části **Konfigurace zprostředkovatele identity** proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/picturepark-tutorial/ic795064.png "Konfigurace zprostředkovatele identity")
   
    a. Klikněte na **Přidat**.
  
    b. Zadejte název konfigurace.
   
    c. Jako výchozí vyberte **Nastavit**.
   
    d. V textovém poli **Identifikátor URI vystavitela** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.
   
    e. V textovém poli **Vytištění palce důvěryhodného vystavitna** vložte hodnotu **kryptografický otisk,** kterou jste zkopírovali z části **Podpisový certifikát SAML.** 

5. Klepněte na tlačítko **JoinDefaultUsersGroup**.

6. Chcete-li nastavit atribut **E-mailová** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` adresa v textovém poli **Nárok,** zadejte a klepněte na tlačítko **Uložit**.

      ![Konfigurace](./media/picturepark-tutorial/ic795065.png "Konfigurace")

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Picturepark.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Picturepark**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Picturepark**.

    ![Odkaz Picturepark v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-picturepark-test-user"></a>Vytvořit uživatele testu Picturepark

Aby bylo možné uživatelům Azure AD přihlásit do Picturepark, musí být zřízena do Picturepark. V případě Picturepark zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Picturepark.**

1. Na panelu nástrojů v horní části klikněte na **Nástroje pro správu**a potom klikněte na **Uživatelé**.
   
    ![Uživatelé](./media/picturepark-tutorial/ic795067.png "Uživatelé")

1. Na kartě **Uživatelé** klepněte na tlačítko **Nový**.
   
    ![Správa uživatelů](./media/picturepark-tutorial/ic795068.png "Správa uživatelů")

1. V dialogovém okně **Vytvořit uživatele** proveďte následující kroky platného uživatele služby Azure Active Directory, kterého chcete zřídit:
   
    ![Vytvořit uživatele](./media/picturepark-tutorial/ic795069.png "Vytvořit uživatele")
   
    a. Do textového pole **E-mailová adresa** `BrittaSimon@contoso.com`zadejte **e-mailovou adresu** uživatele .  
   
    b. Do textových polí **Heslo** a **Potvrdit heslo** zadejte **heslo** Britty Simon. 
   
    c. Do textového pole **Křestní jméno** zadejte **křestní jméno** uživatele **Britta**. 
   
    d. Do textového pole **Příjmení** zadejte **příjmení** uživatele **Simona**.
   
    e. Do textového pole **Společnost** zadejte **název společnosti** uživatele. 
   
    f. V textovém poli **Země** vyberte **zemi nebo oblast** uživatele.
  
    g. Do textového pole **PSČ** zadejte **PSČ** města.
   
    h. Do textového pole **Město** zadejte **název města** uživatele.

    i. Vyberte **jazyk**.
   
    j. Klikněte na **Vytvořit**.

>[!NOTE]
>Ke zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Picturepark nebo rozhraní API poskytovaná pictureparkem.
>

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Picturepark na přístupovém panelu, můžete by měl být automaticky přihlášeni k Picturepark, u kterého nastavíte přiřazovat. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

