---
title: 'Kurz: Integrace služby Azure Active Directory s turboraterem | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088278"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Kurz: Integrace služby Azure Active Directory s turboraterem

V tomto kurzu se dozvíte, jak integrovat TurboRater s Azure Active Directory (Azure AD).

Integrace TurboRater s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TurboRater.
* Můžete povolit, aby vaši uživatelé byli automaticky přihlášeni k TurboRater (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění: na portálu Azure.

Podrobnosti o integraci aplikací softwaru jako služby (SaaS) s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s TurboRater, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Předplatné TurboRater s povoleným jednorázovým přihlášením.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

TurboRater podporuje jednotné přihlašování iniciované idicí IDP( SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Přidání turborateru z Azure Marketplace

Chcete-li nakonfigurovat integraci TurboRater do Azure AD, musíte přidat TurboRater z Azure Marketplace do seznamu spravovaných aplikací SaaS:

1. Přihlaste se k [portálu Azure](https://portal.azure.com?azure-portal=true).
1. V levém podokně vyberte **Azure Active Directory**.

    ![Možnost služby Azure Active Directory](common/select-azuread.png)

1. Přejděte na **položku Podnikové aplikace**a vyberte **možnost Všechny aplikace**.

    ![Možnost Podnikové aplikace](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna **možnost + Nová aplikace.**

    ![Možnost Nová aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **TurboRater**. Ve výsledcích hledání vyberte **TurboRater**a pak vyberte **Přidat,** chcete-li přidat aplikaci.

    ![TurboRater v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí turborateru na základě testovacího uživatele s názvem **B Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v turborateru.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TurboRater, je třeba dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** tak, aby uživatelé mohli tuto funkci používat.
1. **[Nakonfigurujte jednotné přihlašování TurboRater](#configure-turborater-single-sign-on)** tak, aby bylo nakonfigurováno nastavení jednotného přihlášení na straně aplikace.
1. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
1. **[Přiřaďte uživateli testu Azure AD,](#assign-the-azure-ad-test-user)** aby umožnil B. Simonovi používat jednotné přihlašování Azure AD.
1. **[Vytvořte testovacího uživatele TurboRater](#create-a-turborater-test-user)** tak, aby v TurboRateru byl uživatel jménem B. Simon, který je propojený s uživatelem Azure AD s názvem B. Simon.
1. **[Otestujte jednotné přihlášení](#test-single-sign-on)** a ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí turborateru, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **TurboRater** vyberte **Jedno přihlášení**.

    ![Konfigurovat možnost jednotného přihlášení](common/select-sso.png)

1. V podokně **Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

1. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyberte **Upravit** (ikona tužky), chcete-li otevřít podokno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V podokně **Základní konfigurace SAML** postupujte takto:

    ![Informace o doméně turborateru a adresách URL s jedním přihlášením](common/idp-intiated.png)

    1. Do pole **Identifikátor (ID entity)** zadejte adresu URL:

       `https://www.itcdataservices.com`

    1. Do pole **Adresa URL pro odpověď (Adresa URL služby Assertion Consumer Service)** zadejte adresu URL pomocí následujícího vzoru:

       | Prostředí | zprostředkovatele identity |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Živé  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory TurboRater](https://www.getitc.com/support). Můžete také odkazovat na vzory zobrazené v **podokně Základní konfigurace SAML** na webu Azure Portal.

1. V podokně **Nastavit jednotné přihlašování pomocí saml** vyberte v části **Podpisový certifikát SAML** **položku Stáhnout,** chcete-li z daných možností stáhnout **xml metadat federace** a uložit jej do počítače.

    ![Možnost stažení XML metadat federace](common/metadataxml.png)

1. V části **Nastavit TurboRater** zkopírujte adresu URL nebo adresy URL, které potřebujete:

   * **Přihlašovací adresa URL**
   * **Identifikátor azure reklamy**
   * **Adresa URL odhlášení**

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurace jednotného přihlášení turborateru

Chcete-li nakonfigurovat jednotné přihlašování na straně TurboRater, je třeba odeslat stažený xml metadat federace a příslušné zkopírované adresy URL z portálu Azure týmu [podpory TurboRater](https://www.getitc.com/support). Tým TurboRater zajistí, že připojení SSO SAML je správně nastaveno na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovací ho uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal v levém podokně vyberte možnost**Uživatelé služby** >  **Azure Active Directory**   > **Všichni uživatelé**.

    ![Možnosti Uživatelé a Všichni uživatelé](common/users.png)

1. V horní části obrazovky vyberte **+ Nový uživatel**.

    ![Nová možnost uživatele](common/new-user.png)

1. V podokně **Uživatel** postupujte takto:

    ![Podokno Uživatel](common/user-properties.png)

    1. Do pole **Název** zadejte **BSimon**.
  
    1. Do pole **Uživatelské jméno** zadejte **bSimon\@\<\< vašífiremní domény>. prodloužení>**. Například **BSimon\@contoso.com**.

    1. Zaškrtněte políčko **Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat Azure jednotné přihlášení udělením přístupu k TurboRater.

1. Na portálu Azure vyberte **Podnikové aplikace** > **Všechny aplikace** > **TurboRater**.

    ![Podokno podnikové aplikace](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **možnost TurboRater**.

    ![TurboRater v seznamu aplikací](common/all-applications.png)

1. V levém podokně vyberte v části **MANAGE** **položku Uživatelé a skupiny**.

    ![Možnost "Uživatelé a skupiny"](common/users-groups-blade.png)

1. Vyberte **+ Přidat uživatele**a pak v podokně Přidat **přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **B. Simon** a v dolní části podokna zvolte **Vybrat.**

1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, vyberte v podokně **Vybrat roli** příslušnou roli pro uživatele ze seznamu. V dolní části podokna zvolte **Vybrat**.

1. V podokně **Přidat přiřazení** vyberte **Přiřadit**.

### <a name="create-a-turborater-test-user"></a>Vytvoření testovacího uživatele TurboRater

V této části vytvoříte uživatele s názvem B. Simon v TurboRater. Spolupracujte s [týmem podpory TurboRater](https://www.getitc.com/support) a přidejte B. Simona jako uživatele do TurboRateru. Uživatelé musí být vytvořena a aktivována před použitím jednotného přihlášení.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí portálu Moje aplikace.

Když na portálu Moje aplikace vyberete **Možnost TurboRater,** měli byste být automaticky přihlášeni k předplatnému TurboRater, pro které nastavíte jednotné přihlašování. Další informace o portálu Moje aplikace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
