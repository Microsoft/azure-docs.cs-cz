---
title: 'Kurz: Integrace služby Azure Active Directory s ArcGIS Enterprise | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e21b1c72f191f3644975afd511a900667a04ce9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157898"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Kurz: Integrace služby Azure Active Directory s ArcGIS Enterprise

V tomto kurzu se dozvíte, jak integrovat ArcGIS Enterprise s Azure Active Directory (Azure AD).
Integrace ArcGIS Enterprise s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ArcGIS Enterprise.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke společnosti ArcGIS Enterprise (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s ArcGIS Enterprise, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením ArcGIS Enterprise

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.



* ArcGIS Enterprise podporuje **sp a IDP** inicioval sondy
* ArcGIS Enterprise podporuje zřizování uživatelů **just in time**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Přidání ArcGIS Enterprise z galerie

Chcete-li nakonfigurovat integraci ArcGIS Enterprise do Azure AD, musíte přidat ArcGIS Enterprise z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat ArcGIS Enterprise z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ArcGIS Enterprise**, z panelu výsledků vyberte **ArcGIS Enterprise** a klepnutím na **tlačítko Přidat** přidejte aplikaci.

     ![ArcGIS Enterprise v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s [Název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v [Název aplikace].

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [Název aplikace], musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** – chcete-li mít protějšek Britty Simonové v ArcGIS Enterprise, která je propojená s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlášení služby Azure AD pomocí [Název aplikace], proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **ArcGIS Enterprise** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciovaného iniciátoru **Protokolu IDP:**

    ![Informace o jednotném přihlášení k podnikové doméně ArcGIS a adresurl](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`<EXTERNAL_DNS_NAME>.portal`

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klepněte na tlačítko **Nastavit další adresy URL** a proveďte následující krok, pokud chcete aplikaci nakonfigurovat v režimu iniciovaném **službou SP:**

    ![Informace o jednotném přihlášení k podnikové doméně ArcGIS a adresurl](common/metadata-upload-additional-signon.png)

    Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL pro odpověď a přihlašovací adresou URL. Obraťte se na [tým podpory arcgis enterprise client](mailto:support@esri.com) získat tyto hodnoty. Hodnotu Identifikátor získáte z **části Nastavit zprostředkovatele identity**, která je vysvětlena dále v tomto kurzu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Konfigurace jednotného přihlašování ArcGIS Enterprise

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti ArcGIS Enterprise jako správce.

2. Vyberte **možnost Organizace >UPRAVIT NASTAVENÍ**.

    ![Konfigurace arcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

3. Vyberte kartu **Zabezpečení**.

    ![Konfigurace arcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

4. Přejděte dolů na **část Enterprise Logins pomocí** oddílu SAML a vyberte SET ENTERPRISE **LOGIN**.

    ![Konfigurace arcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

5. V části **Nastavit zprostředkovatele identity** proveďte následující kroky:

    ![Konfigurace arcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. V textovém poli **Název** zadejte název, jako je **Test služby Azure Active Directory.**

    b. Do textového pole **URL** vložte hodnotu **url metadat federace aplikace,** kterou jste zkopírovali z portálu Azure.

    c. Klikněte na **Zobrazit upřesňující nastavení** a zkopírujte hodnotu **ID entity** a vložte ji do textového pole **Identifikátor** v části **ArcGIS Enterprise Domain and URL** na webu Azure Portal.
    
    ![Konfigurace arcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klepněte na tlačítko **AKTUALIZOVAT ZPROSTŘEDKOVATELE IDENTITY**.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu arcgis enterprise.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **ArcGIS Enterprise**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **ArcGIS Enterprise**.

    ![Odkaz ArcGIS Enterprise v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-arcgis-enterprise-test-user"></a>Vytvořit testovacího uživatele ArcGIS Enterprise

V této části je uživatel s názvem Britta Simon vytvořen v ArcGIS Enterprise. ArcGIS Enterprise podporuje zřizování uživatelů just-in-time, které je ve výchozím nastavení povoleno. V této části pro vás není žádná položka akce. Pokud uživatel ještě neexistuje v ArcGIS Enterprise, nový je vytvořen po ověření.

> [!Note]
> Pokud potřebujete vytvořit uživatele ručně, obraťte se na [tým podpory ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ArcGIS Enterprise na přístupovém panelu, můžete by měl být automaticky přihlášeni k ArcGIS Enterprise, pro které nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

