---
title: 'Kurz: Integrace Azure Active Directory s portálem pro správu cloudu pro Microsoft Azure | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Portálem pro správu cloudu pro Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e73a1f80e9fbca66bd58c65c45b299950315309d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161258"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Kurz: Integrace Azure Active Directory s portálem pro správu cloudu pro Microsoft Azure

V tomto kurzu se dozvíte, jak integrovat Portál pro správu cloudu pro Microsoft Azure s Azure Active Directory (Azure AD).
Integrace portálu cloudové správy pro Microsoft Azure s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, kdo má přístup k portálu cloudové správy pro Microsoft Azure.
* Uživatelům můžete povolit automatické přihlášení k portálu cloudové správy pro Microsoft Azure (jednotné přihlašování) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s portálem cloudové správy pro Microsoft Azure, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Portál pro správu cloudu pro předplatné microsoft azure s povoleným jedním přihlášením

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Portál pro správu cloudu pro Microsoft Azure **podporuje** sp iniciované sso

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Přidání portálu pro správu cloudu pro Microsoft Azure z galerie

Pokud chcete nakonfigurovat integraci portálu cloudmanagementu pro Microsoft Azure do Azure AD, musíte přidat portál pro správu cloudu pro Microsoft Azure z galerie do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat portál cloudové správy pro Microsoft Azure z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Portál pro správu cloudu pro Microsoft Azure**, z panelu výsledků vyberte Portál pro **správu cloudu pro Microsoft Azure** a kliknutím na tlačítko **Přidat** aplikaci přidejte.

     ![Portál pro správu cloudu pro Microsoft Azure v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí portálu pro správu cloudu pro Microsoft Azure na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je třeba vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem na portálu pro správu cloudu pro Microsoft Azure.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu pro správu cloudu pro Microsoft Azure, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte portál pro správu cloudu pro jednotné přihlašování Microsoft Azure](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte portál pro správu cloudu pro testovacího uživatele Microsoft Azure](#create-cloud-management-portal-for-microsoft-azure-test-user)** – chcete-li mít na portálu pro správu cloudu pro Microsoft Azure protějšek Britty Simonové, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí portálu pro správu cloudu pro Microsoft Azure, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Cloud Management Portal pro Microsoft Azure** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Portál pro správu cloudu pro domény Microsoft Azure a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [portál cloudmanagement portal pro tým podpory klienta Microsoft Azure.](mailto:jczernuszka@newsignature.com) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit cloudovou správu pro Microsoft Azure** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Konfigurace portálu pro správu cloudu pro jednotné přihlašování Microsoft Azure

Chcete-li nakonfigurovat jednotné přihlašování na **portálu Cloud Management Portal pro** stranu Microsoft Azure, musíte odeslat stažený certifikát **(Base64)** a příslušné zkopírované adresy URL z portálu Azure na [portál Cloud Management Portal pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com). Toto nastavení nastaví tak, aby bylo připojení s přizasazené k samovazbě SAML správně nastaveno na obou stranách.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k portálu pro správu cloudu pro Microsoft Azure.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte Portál **pro správu cloudu pro Microsoft Azure**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Portál pro správu cloudu pro Microsoft Azure**.

    ![Odkaz Na portál pro správu cloudu pro Microsoft Azure v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Vytvoření portálu cloudmanagementu pro testovacího uživatele Microsoft Azure

V této části vytvoříte uživatele s názvem Britta Simon na portálu pro správu cloudu pro Microsoft Azure. Spolupracujte s [týmem podpory Cloud Management Portal pro Microsoft Azure](mailto:jczernuszka@newsignature.com) a přidejte uživatele na portál pro správu cloudu pro platformu Microsoft Azure. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Cloud Management Portal pro Microsoft Azure na přístupovém panelu, měli byste být automaticky přihlášeni k portálu pro správu cloudu pro Microsoft Azure, pro které nastavíte přiřazování služeb při přijatí. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

