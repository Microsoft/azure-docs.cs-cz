---
title: 'Kurz: Integrace služby Azure Active Directory s daty periskopu | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Periskopovými daty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ffa0176c4dde6334e70b9a56b066a677d1610b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160193"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Kurz: Integrace služby Azure Active Directory s daty periskopu

V tomto kurzu se dozvíte, jak integrovat periskopová data s Azure Active Directory (Azure AD).
Integrace periskopových dat s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k datům Periskop.
* Můžete povolit uživatelům, aby se automaticky přihlásili k periskopovým datům (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s periskopovými daty, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením Periscope Data

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Periscope Data podporuje **sp** iniciované sso

## <a name="adding-periscope-data-from-the-gallery"></a>Přidání periskopových dat z galerie

Chcete-li nakonfigurovat integraci dat Periskop do Azure AD, musíte přidat periskopová data z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat periskopová data z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Periskopová data**, z panelu výsledků vyberte **Periskopová data** a pak klepnutím na tlačítko **Přidat** aplikaci přidejte.

     ![Periskopová data v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí periskopových dat na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v periskopových datech.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí periskopových dat, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování periskopových dat](#configure-periscope-data-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu periskopových dat](#create-periscope-data-test-user)** – chcete-li mít protějšek Britta Simon v periskopových datech, která je propojena s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí periskopových dat, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikace **Periscope Data** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení o datové doméně periskopu a adresURL periskop](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte libovolnou adresu URL:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Hodnota adresy URL přihlášení není skutečná. Aktualizujte hodnoty skutečnou přihlašovací adresou URL. Obraťte se na [tým podpory klienta periskopdata](mailto:support@periscopedata.com) získat tuto hodnotu a hodnotu identifikátor získáte z **konfigurace periskop data jednotného přihlášení** části, která je vysvětlena dále v kurzu. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na tlačítko Kopírovat, chcete-li zkopírovat **adresu URL metadat federace aplikací** a uložit ji do počítače.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurace jednotného přihlašování k datům periskopu

1. V jiném okně webového prohlížeče se přihlaste k periskopu data jako správce.

2. Otevřete nabídku ozubených kol v levém dolním rohu a otevřete nabídku**Zabezpečení** **fakturace** > a proveďte následující kroky. K těmto nastavením mají přístup pouze správci.

    ![Informace o konfiguraci dat periskopu](./media/periscope-data-tutorial/configure01.png)

    a. Zkopírujte **adresu URL metadat federace aplikací** z kroku #5 **podpisový certifikát SAML** a otevřete ho v prohlížeči. Tím se otevře dokument XML.

    b. V textovém poli **Jednotné přihlášení** vyberte Možnost Azure **Active Directory**.

    c. Najděte značku **SingleSignOnService** a vložte hodnotu **Umístění** do textového pole **URL jednotného přihlašování.**

    d. Najděte značku **SingleLogoutService** a vložte hodnotu **Umístění** do textového pole **Adresa URL SLO.**

    e. Zkopírujte hodnotu **Identifikátor** pro vaši instanci a vložte ji do textového pole **Identifikátor (ID entity)** v části **Základní konfigurace SAML** na webu Azure Portal.

    f. Najděte první značku souboru XML, zkopírujte hodnotu **entityID** a vložte ji do textového pole **Vystavitele.**

    g. Najděte značku **IDPSSODescriptor** s protokolem SAML. V této části vyhledejte značku **KeyDescriptor** s **use=signing**. zkopírujte hodnotu **Certifikátu X509A** a vložte jej do textového pole **Certifikát.**

    h. Weby s více mezerami mohou zvolit výchozí prostor z rozevíracího souboru **Výchozí prostor.** Toto bude prostor, do kterých se přidají noví uživatelé při prvním přihlášení k periskopovým datům a budou zřízeni prostřednictvím jednotného přihlášení služby Active Directory.

    i. Nakonec klepněte na tlačítko **Uložit** a **potvrďte** změnu nastavení přihlašování zadáním **příkazu Odhlášení**.

    ![Informace o konfiguraci dat periskopu](./media/periscope-data-tutorial/configure02.png)

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k periskopdata.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Periskopová data**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Periskopová data**.

    ![Datový odkaz Periscope v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-periscope-data-test-user"></a>Vytvořit uživatele testu periskopových dat

Chcete-li povolit uživatelům Azure AD k přihlášení k datům periskopu, musí být zřízeny do periskopových dat. V Periscope Data zřizování je ruční úlohy.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k periskopových dat jako správce.

2. Klikněte na ikonu **Nastavení** v levém dolním rohu nabídky a přejděte na **Oprávnění**.

    ![Informace o konfiguraci dat periskopu](./media/periscope-data-tutorial/configure03.png)

3. Klikněte na **přidat uživatele** a provést následující kroky:

      ![Informace o konfiguraci dat periskopu](./media/periscope-data-tutorial/configure04.png)

    a. Do pole **Křestní jméno** zadejte křestní jméno uživatele, jako **je Britta**.

    b. Do pole **Příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **E-mail** zadejte e-mail uživatele, jako **\@je brittasimon contoso.com**.

    d. Klepněte na tlačítko **PŘIDAT**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Periscope data na přístupovém panelu, můžete by měl být automaticky přihlášeni k periskopdata, pro které nastavíte přispojené k zámečku. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

