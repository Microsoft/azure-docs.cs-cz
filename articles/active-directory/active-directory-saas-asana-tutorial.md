---
title: 'Kurz: Azure Active Directory integrace s Asana | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 1c4245dbb34fc7e4b3b2722e4a94bffaf4d7f66d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589905"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Azure Active Directory integrace s Asana

V tomto kurzu zjistěte, jak integrovat Asana s Azure Active Directory (Azure AD).

Integrace Asana s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Asana
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Asana (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Asana, potřebujete následující položky:

- Předplatné služby Azure AD
- Asana jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Asana z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-asana-from-the-gallery"></a>Přidání Asana z Galerie
Při konfiguraci integrace Asana do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Asana z galerie.

**Pokud chcete přidat Asana z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Asana**, vyberte **Asana** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Asana podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Asana je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Asana musí navázat.

V Asana, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Asana, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Asana](#create-an-asana-test-user)**  – Pokud chcete mít protějšek Britta Simon v Asana propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Asana.

**Ke konfiguraci Azure AD jednotné přihlašování s Asana, proveďte následující kroky:**

1. Na portálu Azure na **Asana** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. Na **Asana domény a adresy URL** část, proveďte následující kroky:

    ![Asana domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadat adresu URL: `https://app.asana.com/`

    b. V **identifikátor** textovému poli, hodnota typu: `https://app.asana.com/`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. Na **Asana konfigurace** klikněte na tlačítko **konfigurace Asana** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png)

7. V okně jiný prohlížeč přihlášení do aplikace Asana. Nakonfigurovat jednotné přihlašování v Asana, přístup k nastavení pracovního prostoru klikněte na název pracovního prostoru v pravém horním rohu obrazovky. Potom klikněte na  **\<název pracovního prostoru\> nastavení**.

    ![Nastavení jednotného přihlašování k Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. Na **nastavení organizace** okně klikněte na tlačítko **správy**. Potom klikněte na **členy musí přihlásit pomocí SAML** umožňující jednotného přihlašování k konfigurace. Proveďte následující kroky:

    ![Konfigurace nastavení jednotný přihlášení](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. V **přihlašovací adresa URL stránky** textovému poli, Vložit **SAML jeden přihlašování adresa URL služby**.

     b. Klikněte pravým tlačítkem na certifikát si stáhli z portálu Azure a pak otevřete soubor certifikátu pomocí poznámkového bloku nebo upřednostňovaný textový editor. Kopírovat obsah mezi začátku a název certifikátu end a vložte jej do **certifikát X.509** textové pole.

9. Klikněte na **Uložit**. Přejděte na [Asana Průvodce pro nastavení jednotného přihlašování k](https://asana.com/guide/help/premium/authentication#gl-saml) Pokud potřebujete další pomoc.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png)

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Tlačítko Přidat](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png)

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-asana-test-user"></a>Vytvořit uživatele s Asana testu

Cílem této části je vytvoření uživatele v Asana nazývá Britta Simon. Asana podporuje automatické uživatele zřizování, který je ve výchozím nastavení povolené. Můžete najít další podrobnosti o [sem](active-directory-saas-asana-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

V této části vytvoříte volal Britta Simon v Asana uživatele.

1. Na **Asana**, přejděte na **týmy** části na levém panelu. Klikněte na tlačítko znaménko plus.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png)

2. Zadejte e-mailu britta.simon@contoso.com v textovém poli a potom vyberte **pozvat**.

3. Klikněte na tlačítko **odeslat pozvání**. Nový uživatel obdrží e-mail do své e-mailový účet. Uživatel bude muset vytvořit a ověřit účet.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Asana.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Asana, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Asana**.

    ![V seznamu aplikací na Asana odkaz](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je pro testování vaší služby Azure AD jednotné přihlašování.

Přejděte na stránku přihlášení Asana. Do textového pole e-mailovou adresu, vložit e-mailovou adresu britta.simon@contoso.com. Ponechte textové pole hesla v prázdné a pak klikněte na **protokolu v**. Budete přesměrováni na přihlašovací stránku služby Azure AD. Dokončení přihlašovací údaje Azure AD. Nyní jste přihlášeni Asana.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](active-directory-saas-asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png