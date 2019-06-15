---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Soloinsight CloudGate | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Soloinsight CloudGate jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2dc6dcbcdee8df93f34cf4d68b5e08453554bc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090013"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Kurz: Integrace Soloinsight CloudGate jednotného přihlašování s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Soloinsight CloudGate jednotného přihlašování s Azure Active Directory (Azure AD). Při integraci Soloinsight CloudGate jednotného přihlašování s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Soloinsight CloudGate jednotného přihlašování.
* Povolte uživatelům, aby se automaticky nepřihlásil k Soloinsight CloudGate jednotného přihlašování s jejich účty Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Jednotné přihlašování Soloinsight CloudGate jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje jednotné přihlašování Soloinsight CloudGate **SP** jednotné přihlašování zahájené.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Přidání jednotného přihlašování Soloinsight CloudGate z Galerie

Konfigurace integrace Soloinsight CloudGate jednotné přihlašování do služby Azure AD, budete muset přidat Soloinsight CloudGate jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **jednotného přihlašování Soloinsight CloudGate** do vyhledávacího pole.
1. Vyberte **jednotného přihlašování Soloinsight CloudGate** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s jednotným Přihlašováním Soloinsight CloudGate pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Soloinsight CloudGate jednotného přihlašování.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s jednotným Přihlašováním Soloinsight CloudGate, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Soloinsight CloudGate](#configure-soloinsight-cloudgate-sso)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Soloinsight CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)**  mít protějšek Britta Simon Soloinsight CloudGate jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Soloinsight CloudGate jednotného přihlašování** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    1. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.sigateway.com/login`

    1. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v **nakonfigurovat Soloinsight CloudGate jednotného přihlašování jednotného přihlašování** části kurzu.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavit jednotné přihlašování Soloinsight CloudGate** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurace jednotného přihlašování Soloinsight CloudGate

1. K automatizaci konfigurace v rámci Soloinsight CloudGate jednotného přihlašování, budete muset nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **Soloinsight CloudGate nastavení jednotného přihlašování** nasměruje na Soloinsight CloudGate jednotného přihlašování aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Soloinsight CloudGate jednotného přihlašování. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 8.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit jednotné přihlašování Soloinsight CloudGate ručně, otevřete nové okno webového prohlížeče a přihlaste se jako správce společnosti lokalitu Soloinsight CloudGate jednotného přihlašování a proveďte následující kroky:

4. K získání hodnot, které se mají vložit na webu Azure Portal při konfiguraci základní SAML, přihlaste se pomocí svých přihlašovacích údajů na CloudGate webový portál a přístup k nastavení jednotného přihlašování, které najdete v následující cestě **Domů > Správa > Nastavení systému > Obecné**.

    ![Nastavení jednotného přihlašování CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **Adresa URL zákazníka SAML**

    * Zkopírujte odkazy k dispozici na **adresu URL příjemce Saml** a **adresy URL pro přesměrování** polí a vložit je na webu Azure Portal **základní konfiguraci SAML** části  **Identifikátor (Entity ID)** a **adresy URL odpovědi** polí v uvedeném pořadí.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Podpisový certifikát SAML**

    * Přejít na zdrojový soubor certifikátu (Base64), který jste stáhli z webu Azure portal podpisový certifikát SAML, seznamy a klikněte pravým tlačítkem myši na něj. Zvolte **upravovat pomocí poznámkového bloku ++** možnost ze seznamu. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Zkopírujte obsah v souboru poznámkového bloku ++ certifikát (Base64).

        ![Kopie certifikátu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Vložte obsah v nastavení jednotného přihlašování k portálu CloudGate webu **certifikát** pole a klikněte na tlačítko Uložit.

        ![Portál Certificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Výchozí skupiny**

    * Vyberte **správce Business** z rozevíracího seznamu **výchozí skupinu** možnost CloudGate webovém portálu

        ![Výchozí skupiny](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identifikátor AD a přihlašovací adresa URL**

    * Zkopírovaný **přihlašovací adresa URL** z portálu Azure portal **nastavit jednotné přihlašování Soloinsight CloudGate** konfigurace mají být zadány v části Nastavení jednotného přihlašování k CloudGate webu portálu.

    * Vložit **přihlašovací adresa URL** odkazu z webu Azure portal na webovém portálu CloudGate **přihlašovací adresa URL AD** pole.

    * Vložit **Azure AD identifikátor** odkazu z webu Azure portal na webovém portálu CloudGate **AD identifikátor** pole

        ![Přihlášení k AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá Britta Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Soloinsight CloudGate jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **jednotného přihlašování Soloinsight CloudGate**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Vytvořit testovacího uživatele Soloinsight CloudGate jednotného přihlašování

Chcete-li vytvořit testovacího uživatele, vyberte **zaměstnanci** v hlavní nabídce CloudGate webový portál a vyplnit formulář přidat nové zaměstnance. Úroveň autoritu, která má být přiřazena testovacího uživatele je **správce Business** klikněte na **vytvořit** po všechna povinná pole jsou vyplněna.

![Zaměstnanec testu](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO

Když vyberete dlaždici Soloinsight CloudGate jednotného přihlašování na přístupovém panelu, můžete by měl být automaticky přihlášeni ke Soloinsight CloudGate jednotného přihlašování, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)