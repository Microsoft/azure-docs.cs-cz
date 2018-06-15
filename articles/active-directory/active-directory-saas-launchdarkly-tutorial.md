---
title: 'Kurz: Azure Active Directory integrace s LaunchDarkly | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: f6611009ae10bf7a0d5c7f20f3ad26e9d41e456e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340705"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Kurz: Azure Active Directory integrace s LaunchDarkly

V tomto kurzu zjistěte, jak integrovat LaunchDarkly s Azure Active Directory (Azure AD).

Integrace LaunchDarkly s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k LaunchDarkly.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LaunchDarkly (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LaunchDarkly, potřebujete následující položky:

- Předplatné služby Azure AD
- LaunchDarkly jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LaunchDarkly z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-launchdarkly-from-the-gallery"></a>Přidání LaunchDarkly z Galerie
Při konfiguraci integrace LaunchDarkly do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS LaunchDarkly z galerie.

**Pokud chcete přidat LaunchDarkly z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **LaunchDarkly**, vyberte **LaunchDarkly** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![LaunchDarkly v seznamu výsledků](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s LaunchDarkly podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v LaunchDarkly je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v LaunchDarkly musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LaunchDarkly, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele LaunchDarkly](#create-a-launchdarkly-test-user)**  – Pokud chcete mít protějšek Britta Simon v LaunchDarkly propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci LaunchDarkly.

**Ke konfiguraci Azure AD jednotné přihlašování s LaunchDarkly, proveďte následující kroky:**

1. Na portálu Azure na **LaunchDarkly** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. Na **LaunchDarkly domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![LaunchDarkly domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL: `app.launchdarkly.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Adresa URL odpovědi hodnota není skutečné. Hodnota bude aktualizován skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu.

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![LaunchDarkly domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://app.launchdarkly.com`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_400.png)
    
7. Na **LaunchDarkly konfigurace** klikněte na tlačítko **konfigurace LaunchDarkly** otevřete **konfigurovat přihlášení** okno. Kopírování **jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti LaunchDarkly.

9. Vyberte **nastavení účtu** z levého navigačního panelu.

    ![Konfigurace LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure1.png)

10. Klikněte na tlačítko **zabezpečení** kartě.

    ![Konfigurace LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure2.png)

11. Klikněte na tlačítko **povolení jednotného přihlašování k** a potom **konfigurace SAML upravit**.

    ![Konfigurace LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure3.png)

12. Na **upravit konfiguraci SAML** část, proveďte následující kroky:

    ![Konfigurace LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/configure4.png)

    a. Kopírování **adresa URL služby SAML příjemce** instance a vložte ji do textového pole Adresa URL odpovědi v **LaunchDarkly domény a adresy URL** části na portálu Azure.

    b. V **přihlašovací adresa URL** textovému poli, Vložit **jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure.

    c. Otevřete certifikát stažený z portálu Azure do programu Poznámkový blok, kopírovat obsah a vložte ji do **certifikát X.509** pole nebo je můžete přímo nahrát certifikát kliknutím **nahrát některou**.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-launchdarkly-test-user"></a>Vytvoření zkušebního uživatele LaunchDarkly

Cílem této části je vytvoření uživatele v LaunchDarkly nazývá Britta Simon. LaunchDarkly podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k LaunchDarkly, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory LaunchDarkly klienta](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu LaunchDarkly.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon LaunchDarkly, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **LaunchDarkly**.

    ![V seznamu aplikací na LaunchDarkly odkaz](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LaunchDarkly na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci LaunchDarkly.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_203.png

