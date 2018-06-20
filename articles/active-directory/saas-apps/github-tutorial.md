---
title: 'Kurz: Azure Active Directory integrace s Githubu | Microsoft Docs'
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
ms.openlocfilehash: 9263be24f883d8a02735e757f3ac02b54960f61f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227390"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Kurz: Azure Active Directory integrace s Githubu

V tomto kurzu zjistěte, jak integrovat Githubu s Azure Active Directory (Azure AD).

Integrace Githubu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup ke Githubu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k webu GitHub (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Githubu, potřebujete následující položky:

- Předplatné služby Azure AD
- Githubu jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Githubu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-github-from-the-gallery"></a>Přidání Githubu z Galerie
Při konfiguraci integrace Githubu do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Githubu z galerie.

**Pokud chcete přidat Githubu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Githubu**, vyberte **Githubu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![GitHub v seznamu výsledků](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Githubu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Githubu, je nutné stanovit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Githubu](#create-a-github-test-user)**  – Pokud chcete mít protějšek Britta Simon v Githubu, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Githubu.

**Ke konfiguraci Azure AD jednotné přihlašování s Githubu, proveďte následující kroky:**

1. Na portálu Azure na **Githubu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/github-tutorial/tutorial_github_samlbase.png)

3. Na **Githubu domény a adresy URL** část, proveďte následující kroky:

    ![GitHub domény a adresy URL jednotné přihlašování informace](./media/github-tutorial/tutorial_github_url.png)

    a. V **přihlásit na adrese URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://github.com/orgs/<entity-id>/sso`

    b. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset tyto hodnoty aktualizovat skutečné znakem na adresu URL a identifikátor. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Přejděte k části GitHub správce se načtení těchto hodnot.

4. Na **uživatelské atributy** vyberte **uživatelský identifikátor** jako user.mail.

    ![Konfigurovat jednotné přihlašování](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/github-tutorial/tutorial_github_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/github-tutorial/tutorial_general_400.png)

7. Na **Githubu konfigurace** klikněte na tlačítko **konfigurace Githubu** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Githubu](./media/github-tutorial/tutorial_github_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se jako správce k organizace webu GitHub.

9. Přejděte na **nastavení** a klikněte na tlačítko **zabezpečení**

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Zkontrolujte **ověřování povolit SAML** pole odhalil konfigurační pole jednotné přihlašování. Pak můžete použijte jednu hodnotu adresy URL přihlašování k aktualizaci adresy jednotného přihlašování konfigurace služby Azure AD.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Vyplňte následující pole:

    a. V **přihlásit na adrese URL** textovému poli, vložte **SAML jeden přihlašovací adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure.

    b. V **vystavitele** textovému poli, vložte **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure.

    c. Otevřete certifikát stažený z portálu Azure v programu Poznámkový blok, vložte obsah do **veřejný certifikát** textové pole.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Klikněte na **Konfigurace testu SAML** zkontrolujte, že žádná chyb při ověřování nebo chyby během jednotného přihlašování.

    ![Nastavení](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/github-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/github-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/github-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-github-test-user"></a>Vytvoření zkušebního uživatele Githubu

Cílem této části je vytvoření uživatele volal Britta Simon v Githubu. GitHub podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](github-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Githubu jako správce.

2. Klikněte na tlačítko **osoby**.

    ![Lidé](./media/github-tutorial/tutorial_github_config_github_08.png "osoby")

3. Klikněte na tlačítko **pozvání člen**.

    ![Uživatele pozvat](./media/github-tutorial/tutorial_github_config_github_09.png "pozvat uživatele")

4. Na **pozvání člen** dialogové okno proveďte následující kroky:

    a. V **e-mailu** textovému poli, zadejte e-mailovou adresu účtu Britta Simon.

    ![Pozvěte lidi, kteří](./media/github-tutorial/tutorial_github_config_github_10.png "pozvat uživatele")

    b. Klikněte na tlačítko **odeslat pozvánky**.

    ![Pozvěte lidi, kteří](./media/github-tutorial/tutorial_github_config_github_11.png "pozvat uživatele")

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ke Githubu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Githubu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Githubu**.

    ![V seznamu aplikací na Githubu odkaz](./media/github-tutorial/tutorial_github_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Githubu na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Githubu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

