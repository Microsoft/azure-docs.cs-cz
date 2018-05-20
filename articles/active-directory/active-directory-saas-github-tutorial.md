---
title: 'Kurz: Azure Active Directory integrace s Githubu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Githubu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ef9d0bb112738e9abea5894fb0f321368b7fc26e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Kurz: Azure Active Directory integrace s Githubu

V tomto kurzu zjistěte, jak integrovat Githubu s Azure Active Directory (Azure AD).

Integrace Githubu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke Githubu
- Můžete povolit uživatelům, aby automaticky získat přihlášení k webu GitHub (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Githubu, potřebujete následující položky:

- Předplatné služby Azure AD
- Githubu jednotného přihlašování povolené předplatné


> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.


Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Githubu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-github-from-the-gallery"></a>Přidání Githubu z Galerie
Při konfiguraci integrace Githubu do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Githubu z galerie.

**Pokud chcete přidat Githubu z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **webu GitHub.com**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. Na panelu výsledků vyberte **Githubu**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Githubu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Githubu, je nutné stanovit.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Githubu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Githubu, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Githubu](#creating-a-GitHub-test-user)**  – Pokud chcete mít protějšek Britta Simon v Githubu, propojené služby Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci Githubu.

**Ke konfiguraci Azure AD jednotné přihlašování s Githubu, proveďte následující kroky:**

1. Na portálu Azure Management portal na **Githubu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Na **Githubu domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte hodnotu jako: `https://github.com/orgs/<entity-id>/sso`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné Sing na adresu URL a identifikátor. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Přejděte k části GitHub správce se načtení těchto hodnot. 

4. Na **uživatelské atributy** vyberte **uživatelský identifikátor** jako user.mail.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Na **SAML podpisový certifikát** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. Na **vytvořit nový certifikát** dialogové okno, klikněte na ikonu kalendáři a vyberte **datum vypršení platnosti**. Pak klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Na **SAML podpisový certifikát** vyberte **aktivujte nový certifikát** a klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. V místní nabídce **certifikát výměny** okně klikněte na tlačítko **OK**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. Na **Githubu konfigurace** klikněte na tlačítko **konfigurace Githubu** otevřete **konfigurovat přihlášení** okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. V okně prohlížeče jiný web Přihlaste se jako správce k organizace webu GitHub.

12. Přejděte na **nastavení** a klikněte na tlačítko **zabezpečení**

    ![Nastavení](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Zkontrolujte **ověřování povolit SAML** pole odhalil konfigurační pole jednotné přihlašování. Pak můžete použijte jednu hodnotu adresy URL přihlašování k aktualizaci adresy jednotného přihlašování konfigurace služby Azure AD.

    ![Nastavení](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Vyplňte následující pole:

    a. **Přihlaste na adrese URL**: Zadejte **SAML jednotné přihlašování adresa URL služby** z **konfigurace Githubu** části na Azure AD

    b. **Vystavitel**: Zadejte **SAML Entity ID** z **konfigurace Githubu** části na Azure AD

    c. **Veřejný certifikát**: Otevřete stažený certifikát z Azure AD v programu Poznámkový blok a zkopírujte obsah, včetně "Začít certifikát" a "END CERTIFICATE"

    ![Nastavení](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Klikněte na **Konfigurace testu SAML** zkontrolujte, že žádná chyb při ověřování nebo chyby během jednotného přihlašování.

    ![Nastavení](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 


### <a name="creating-a-github-test-user"></a>Vytvoření zkušebního uživatele Githubu

Pokud chcete povolit uživatelům Azure AD přihlášení na Githubu, musí být zřízená na Githubu.  
V případě Githubu zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Githubu jako správce.

2. Klikněte na tlačítko **osoby**.

    ![Lidé](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "osoby")

3. Klikněte na tlačítko **pozvání člen**.

    ![Uživatele pozvat](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "pozvat uživatele")

4. Na **pozvání člen** dialogové okno proveďte následující kroky:

    a. V **e-mailu** textovému poli, zadejte e-mailovou adresu účtu Britta Simon.

    ![Pozvěte lidi, kteří](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "pozvat uživatele")
    
    b. Klikněte na tlačítko **odeslat pozvánky**.

    ![Pozvěte lidi, kteří](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "pozvat uživatele")

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup ke Githubu.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Githubu, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **webu GitHub.com**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    


### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Githubu na přístupovém panelu, jste měli získat přihlášení k aplikaci Githubu. Se budete přihlašovat jako účet, ale pak nutné se přihlásit pomocí svého osobního účtu organizace.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
