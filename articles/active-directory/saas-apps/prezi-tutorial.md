---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Prezi | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 2f3e540174643f20c87396b8568f6e5b0a1ab16d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí Prezi

V tomto kurzu se dozvíte, jak integrovat Prezi s Azure Active Directory (Azure AD). Když integrujete Prezi s Azure AD, můžete:

* Řízení přístupu k Prezi ve službě Azure AD.
* Umožněte uživatelům, aby se automaticky přihlásili k Prezi pomocí svých účtů Azure AD.
* Spravujte své účty v Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Prezi je povolené s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Prezi podporuje jednotné přihlašování (SSO) spouštěné v SP a IDP.
* Prezi podporuje zřizování uživatelů za běhu.
* Po nakonfigurování Prezi můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. Další informace najdete v tématu [vymáhání řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-prezi-from-the-gallery"></a>Přidání Prezi z Galerie

Pokud chcete nakonfigurovat integraci Prezi do služby Azure AD, musíte přidat Prezi z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V podokně úplně vlevo vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Prezi** .
1. Na panelu výsledků vyberte **Prezi** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Prezi

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Prezi pomocí testovacího uživatele s názvem B. Simon. Jednotné přihlašování umožňuje vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Prezi.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Prezi, dokončete tyto stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
    1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) povolení B. Simon pro použití jednotného přihlašování služby Azure AD.
1. [NAKONFIGURUJTE jednotné](#configure-prezi-sso) přihlašování Prezi pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. [Vytvořte Prezi testovacího uživatele](#create-a-prezi-test-user) , který bude mít protějšek B. Simon v Prezi, která je propojená s reprezentací uživatele Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Postup při povolení jednotného přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Prezi** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu **Upravit** a upravte nastavení **základní konfigurace SAML**.

   ![Upravit základní nastavení konfigurace SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** nemusí uživatel dělat žádný krok, protože aplikace už je předem integrovaná s Azure.

1. Vyberte **nastavit další adresy URL**a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované pomocí **SP**:

    Do pole **přihlašovací adresa** URL zadejte adresu URL: `https://prezi.com/login/sso/` .

1. Vyberte **Uložit**.

1. Aplikace Prezi očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Atributy uživatele & deklarací identity](common/default-attributes.png)

1. Aplikace Prezi také očekává několik dalších atributů, které se mají zpětně předat v odpovědi SAML, jak je znázorněno zde. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.
    
    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | given_name | User. křestní jméno |
    | family_name | User. příjmení |

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)**. Vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Prezi** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V podokně Azure Portal v levém podokně vyberte možnost **Azure Active Directory**. Klikněte na **Uživatelé**a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech uživatele proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.
   1. Do pole **uživatelské jméno** zadejte `username@companydomain.extension` například `B.Simon@contoso.com` .
   1. Zaškrtněte políčko **Zobrazit heslo** . Zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Prezi.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Prezi**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a potom klikněte na **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-prezi-sso"></a>Konfigurace jednotného přihlašování Prezi

1. V jiném okně webového prohlížeče se přihlaste k Prezi pomocí svého týmového účtu a otevřete [konzolu pro správu](https://prezi.com/organizations/manage).

1. V **konzole pro správu**vyberte kartu **Nastavení** .

    ![Karta nastavení](./media/prezi-tutorial/settings-image.png)

1. Pokud chcete povolit jednotné přihlašování, přepněte do části **Single Sign-On (SSO)** a zapněte přepínač.
    
    ![Přepínač jednotného Sign-On (SSO)](./media/prezi-tutorial/single-signon.png)

1. V části **jednotné přihlašování (SSO)** použijte následující postup:

    ![Oddíl jednotného přihlašování (SSO)](./media/prezi-tutorial/configuration.png)

    1. Do pole **identifikátor nebo adresa URL vydavatele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **koncový bod SAML 2,0 (http)** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Otevřete stažený **certifikát (Base64)** z Azure Portal do poznámkového bloku. Zkopírujte obsah certifikátu a vložte ho do pole **certifikát (X. 509)** .

    1. Vyberte **Uložit**.

### <a name="create-a-prezi-test-user"></a>Vytvořit testovacího uživatele v Prezi

V této části se v Prezi vytvoří uživatel s názvem Britta Simon. Prezi podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V tomto oddílu není žádná položka akce. Pokud uživatel ještě v Prezi neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci služby Azure AD SSO pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Prezi, měli byste se automaticky přihlásit k účtu Prezi, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Vyzkoušejte si Prezi s Azure AD](https://aad.portal.azure.com/)
- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Jak chránit Prezi pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

