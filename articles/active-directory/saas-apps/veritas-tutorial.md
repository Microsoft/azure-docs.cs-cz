---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Vault.cloud Veritas Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Vault.cloud Veritas Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: 4ff282b3db4689ceaf5fa27b57c82cb05025712e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449093"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Vault.cloud Veritas Enterprise

V tomto kurzu se dozvíte, jak integrovat Veritas Enterprise Vault.cloud jednotného přihlašování s Azure Active Directory (Azure AD).

Integrace Veritas Enterprise Vault.cloud jednotného přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Veritas Enterprise Vault.cloud jednotného přihlašování
- Uživatele, aby automaticky získat přihlášení k Veritas Enterprise Vault.cloud jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Vault.cloud Enterprise Veritas, potřebujete následující položky:

- S předplatným služby Azure AD
- Veritas Enterprise Vault.cloud SSO jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování Vault.cloud Enterprise Veritas z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Přidání jednotného přihlašování Vault.cloud Enterprise Veritas z Galerie
Pokud chcete nakonfigurovat integraci jednotného přihlašování Vault.cloud Veritas organizace do služby Azure AD, budete muset přidat z Galerie Veritas Enterprise Vault.cloud jednotného přihlašování na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Veritas Enterprise Vault.cloud jednotné přihlašování z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Veritas Enterprise Vault.cloud SSO**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/tutorial_veritas_search.png)

1. Na panelu výsledků vyberte **Veritas Enterprise Vault.cloud SSO**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Veritas Enterprise Vault.cloud jednotného přihlašování na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Veritas Enterprise Vault.cloud jednotného přihlašování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Veritas Enterprise Vault.cloud jednotného přihlašování je potřeba navázat.

V Veritas Enterprise Vault.cloud jednotného přihlašování, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Vault.cloud Veritas organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Veritas Enterprise Vault.cloud jednotného přihlašování](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Veritas Enterprise Vault.cloud SSO, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Veritas Enterprise Vault.cloud jednotného přihlašování.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Vault.cloud Enterprise Veritas, proveďte následující kroky:**

1. Na webu Azure Portal na **Veritas Enterprise Vault.cloud SSO** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_veritas_samlbase.png)

1. Na **Veritas Enterprise Vault.cloud jednotného přihlašování k doméně a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_veritas_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. V **identifikátor** textové pole, použijte adresu URL podle datového centra

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|

    c. V **adresy URL odpovědi** textové pole, použijte adresu URL podle datového centra

    | Datové centrum| zprostředkovatele identity |
    |----------|----|
    | Severní Amerika| `https://auth.lax.archivecloud.net` |
    | Evropa | `https://auth.ams.archivecloud.net` |
    | Asie a Tichomoří| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Veritas Enterprise Vault.cloud jednotného přihlašování Client](https://www.veritas.com/support/.html) tuto výhodu získáte. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_veritas_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_general_400.png)

1. Na **konfiguraci jednotného přihlašování Vault.cloud Enterprise Veritas** klikněte na tlačítko **konfigurace SSO Vault.cloud Veritas Enterprise** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_veritas_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Veritas Enterprise Vault.cloud SSO** straně, je nutné odeslat na stažený **Certificate(Base64)** a **SAML jednotné přihlašování – adresa URL služby** k [tým podpory jednotného přihlašování Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/veritas-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Vytvoření zkušebního uživatele Veritas Enterprise Vault.cloud jednotného přihlašování

V této části vytvořte uživatele Britta Simon v Vault.cloud podnikové jednotné přihlašování. Práce s [tým podpory jednotného přihlašování Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html) přidat uživatele na platformě Vault.cloud podnikové jednotné přihlašování. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Veritas Enterprise Vault.cloud jednotného přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Veritas Enterprise Vault.cloud jednotného přihlašování, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Veritas Enterprise Vault.cloud SSO**.

    ![Konfigurace jednotného přihlašování](./media/veritas-tutorial/tutorial_veritas_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Veritas Enterprise Vault.cloud jednotného přihlašování na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Veritas Enterprise Vault.cloud jednotného přihlašování.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/veritas-tutorial/tutorial_general_01.png
[2]: ./media/veritas-tutorial/tutorial_general_02.png
[3]: ./media/veritas-tutorial/tutorial_general_03.png
[4]: ./media/veritas-tutorial/tutorial_general_04.png

[100]: ./media/veritas-tutorial/tutorial_general_100.png

[200]: ./media/veritas-tutorial/tutorial_general_200.png
[201]: ./media/veritas-tutorial/tutorial_general_201.png
[202]: ./media/veritas-tutorial/tutorial_general_202.png
[203]: ./media/veritas-tutorial/tutorial_general_203.png

