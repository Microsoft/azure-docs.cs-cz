---
title: 'Kurz: Integrace Azure Active Directory s Githubem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Githubu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7f932346cdfd6363bd76f8167b8598d3a1199de3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427784"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Kurz: Integrace Azure Active Directory s Githubem

V tomto kurzu se dozvíte, jak k integraci Githubu s Azure Active Directory (Azure AD).

Integrace Githubu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke Githubu.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného ke Githubu (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Githubem, potřebujete následující položky:

- S předplatným služby Azure AD
- Na Githubu jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Githubu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-github-from-the-gallery"></a>Přidání Githubu z Galerie
Pokud chcete nakonfigurovat integraci Githubu do služby Azure AD, budete muset přidat GitHub na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání Githubu z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Githubu**vyberte **Githubu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![GitHub v seznamu výsledků](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Githubu podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Githubu je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě GitHub.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Githubu](#create-a-github-test-user)**  – Pokud chcete mít protějšek Britta Simon v Githubu, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Githubu.

**Ke konfiguraci Azure AD jednotné přihlašování s Githubem, proveďte následující kroky:**

1. Na webu Azure Portal na **Githubu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/github-tutorial/tutorial_github_samlbase.png)

1. Na **Githubu domény a adresy URL** části, proveďte následující kroky:

    ![GitHub domény a adresy URL jednotného přihlašování – informace](./media/github-tutorial/tutorial_github_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://github.com/orgs/<entity-id>/sso`

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Přejděte do části správce Githubu k načtení těchto hodnot.

1. Na **atributy uživatele** vyberte **identifikátor uživatele** jako user.mail.

    ![Konfigurace jednotného přihlašování](./media/github-tutorial/tutorial_github_attribute_new01.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/github-tutorial/tutorial_github_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/github-tutorial/tutorial_general_400.png)

1. Na **Githubu konfigurace** klikněte na tlačítko **konfiguraci Githubu** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Githubu](./media/github-tutorial/tutorial_github_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce webu organizace GitHub.

1. Přejděte do **nastavení** a klikněte na tlačítko **zabezpečení**

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_03.png)

1. Zkontrolujte **ověřování povolit SAML** pole odhalení pole Konfigurace jednotného přihlašování. Potom použijte jednu hodnotu adresy URL přihlašování aktualizovat adresy jednotného přihlašování – konfigurace služby Azure AD.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_13.png)

1. Vyplňte následující pole:

    a. V **přihlašovací adresa URL** vložit do textového pole **SAML pro jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **vystavitele** vložit do textového pole **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, vložte obsah do **veřejný certifikát** textového pole.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_051.png)

1. Klikněte na **Konfigurace testu SAML** potvrdit, že žádné chyby ověření nebo chyb během jednotného přihlašování.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_06.png)

1. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/github-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/github-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/github-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-github-test-user"></a>Vytvoření zkušebního uživatele Githubu

Cílem této části je vytvořte uživatele Britta Simon v Githubu. GitHub podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](github-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se na web GitHub společnosti jako správce.

1. Klikněte na tlačítko **lidé**.

    ![Lidé](./media/github-tutorial/tutorial_github_config_github_08.png "osoby")

1. Klikněte na tlačítko **pozvání členů**.

    ![Pozvat uživatele](./media/github-tutorial/tutorial_github_config_github_09.png "pozvat uživatele")

1. Na **pozvání členů** dialogového okna stránky, proveďte následující kroky:

    a. V **e-mailu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    ![Pozvat](./media/github-tutorial/tutorial_github_config_github_10.png "pozvat")

    b. Klikněte na tlačítko **poslat pozvánku**.

    ![Pozvat](./media/github-tutorial/tutorial_github_config_github_11.png "pozvat")

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke Githubu.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit ke Githubu, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Githubu**.

    ![Odkaz na Githubu v seznamu aplikací](./media/github-tutorial/tutorial_github_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici GitHub na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Githubu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

