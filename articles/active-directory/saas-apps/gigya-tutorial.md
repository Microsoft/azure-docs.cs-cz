---
title: 'Kurz: Integrace služby Azure Active Directory s Gigyou | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Gigyou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 824e9c459df75ea0307fe314fbf4118cca6e69d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76841757"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Kurz: Integrace služby Azure Active Directory s Gigyou

V tomto kurzu se dozvíte, jak integrovat Gigya s Azure Active Directory (Azure AD).
Integrace Gigya s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Gigya.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k Gigya (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Gigya, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Gigya předplatné s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Gigya podporuje **SP** inicioval SSO

## <a name="adding-gigya-from-the-gallery"></a>Přidání Gigya z galerie

Chcete-li nakonfigurovat integraci Gigya do Azure AD, musíte přidat Gigya z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li gigyu přidat z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Gigya**, z panelu výsledků vyberte **Gigya** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Gigya v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s Gigya na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Gigya.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí gigya, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace gigya jednotného přihlášení](#configure-gigya-single-sign-on)** - pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte gigya testovací ho uživatele](#create-gigya-test-user)** – chcete-li mít protějšek Britta Simon v Gigya, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí gigya, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Gigya** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Gigya Doména a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`http://<companyname>.gigya.com`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta Gigya,](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Gigyu** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-gigya-single-sign-on"></a>Konfigurace jednotného přihlášení Gigya

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Gigya jako správce.

2. Přejděte na ** \> Nastavení přihlášení SAML**a klikněte na tlačítko **Přidat.**
   
    ![Přihlášení saml](./media/gigya-tutorial/ic789532.png "Přihlášení saml")

3. V části **Přihlášení SAML** proveďte následující kroky:
   
    ![Konfigurace SAML](./media/gigya-tutorial/ic789533.png "Konfigurace SAML")
   
    a. Do textového pole **Název** zadejte název konfigurace.
   
    b. V textovém poli **Vystavitena** vložte hodnotu **identifikátoru Azure Ad,** kterou jste zkopírovali z webu Azure Portal. 
   
    c. V textovém poli **adresy URL služby jednotného přihlašování** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z webu Azure Portal.
   
    d. V textovém poli **Formát ID názvu** vložte hodnotu **formátu identifikátoru názvu,** kterou jste zkopírovali z webu Azure Portal.
   
    e. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Certifikát X.509.**
   
    f. Klepněte na **tlačítko Uložit nastavení**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu gigya.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak **gigyu**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Gigya**.

    ![Odkaz Gigya v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-gigya-test-user"></a>Vytvořit gigya testovacího uživatele

Aby bylo možné povolit uživatelům Azure AD k přihlášení do Gigya, musí být zřízena do Gigya. V případě Gigya zřizování je ruční úlohy.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Chcete-li zřídit uživatelské účty, proveďte následující kroky:

1. Přihlaste se na web společnosti **Gigya** jako správce.

2. Přejděte na ** \> Správce spravovat uživatele**a klikněte na **Pozvat uživatele**.
   
    ![Správa uživatelů](./media/gigya-tutorial/ic789535.png "Správa uživatelů")

3. V dialogovém okně Pozvat uživatele proveďte následující kroky:
   
    ![Pozvat uživatele](./media/gigya-tutorial/ic789536.png "Pozvat uživatele")
   
    a. Do textového pole **E-mail** zadejte e-mailový alias platného účtu Azure Active Directory, který chcete zřídit.
    
    b. Klepněte na **tlačítko Pozvat uživatele**.
      
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz pro potvrzení účtu před tím, než se stane aktivním.
    > 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Gigya na přístupovém panelu, můžete by měl být automaticky přihlášeni k Gigya, u kterého nastavíte přivytváření. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

