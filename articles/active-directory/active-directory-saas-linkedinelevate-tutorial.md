---
title: 'Kurz: Azure Active Directory integrace s zvýšení oprávnění LinkedIn | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zvýšení oprávnění LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5b940ac896f95ec796179f44e0dd11dcf6184464
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589385"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Kurz: Azure Active Directory integrace s LinkedIn zvýšení oprávnění

V tomto kurzu zjistěte, jak integrovat LinkedIn zvýšení oprávnění v Azure Active Directory (Azure AD).

Integrace zvýšení oprávnění LinkedIn s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke zvýšení oprávnění LinkedIn
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LinkedIn zvýšení oprávnění (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LinkedIn zvýšení oprávnění, potřebujete následující položky:

- Předplatné služby Azure AD
- Zvýšení oprávnění LinkedIn jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LinkedIn zvýšení oprávnění z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Přidání LinkedIn zvýšení oprávnění z Galerie
Chcete-li nakonfigurovat integraci zvýšení oprávnění LinkedIn do služby Azure AD, přidejte LinkedIn zvýšení oprávnění z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat LinkedIn zvýšení oprávnění z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **zvýšení oprávnění LinkedIn**. Na panelu výsledků klikněte na **zvýšení oprávnění LinkedIn** tuto aplikaci přidat.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s LinkedIn zvýšení oprávnění na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v LinkedIn zvýšení oprávnění je pro uživatele ve službě Azure AD. Jinými slovy musí navázat odkaz vztah mezi uživatele Azure AD a související uživatelské v LinkedIn zvýšení oprávnění.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v LinkedIn zvýšení oprávnění.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LinkedIn zvýšení oprávnění, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele zvýšení oprávnění LinkedIn](#creating-a-linkedin-elevate-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci LinkedIn zvýšení oprávnění.

**Ke konfiguraci Azure AD jednotné přihlašování s LinkedIn zvýšení oprávnění, proveďte následující kroky:**

1. Na portálu Azure Management portal na **zvýšení oprávnění LinkedIn** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. V okně prohlížeče jiný web přihlášení jako správce klienta LinkedIn zvýšení oprávnění.

4. V **centra účtů**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **zvýšení - zvýšení oprávnění Test AAD** z rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Klikněte na **nebo klikněte na tlačítko sem můžete načíst a zkopírujte jednotlivých polí z formuláře** a zkopírujte **Entity Id** a **adresu Url pro přístup k příjemce Assertion (ACS)**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. Na portálu Azure v části **LinkedIn zvýšení oprávnění domény a adresy URL**, proveďte následující kroky, pokud chcete nakonfigurovat jednotné přihlašování v **IdP iniciované** režimu

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. V **identifikátor** textovému poli, zadejte **Entity ID** zkopírovat z portálu LinkedIn 

    b. V **adresa URL odpovědi** textovému poli, zadejte **adresu Url pro přístup k příjemce Assertion (ACS)** zkopírovat z portálu LinkedIn

7. Pokud chcete nakonfigurovat jednotné přihlašování v **iniciované SP**, klikněte na možnost zobrazit Advanced adresy URL v části Konfigurace a konfigurovat přihlášení na adresu URL s vzoru následující:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 

8. Zvýšení oprávnění LinkedIn aplikace očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento. Výchozí hodnota **uživatelský identifikátor** je **user.userprincipalname** ale zvýšení oprávnění LinkedIn očekává to nejde mapovat pomocí e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu, nebo použijte hodnotu odpovídajícího atributu na základě konfigurace vaší organizace.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. V **uživatelské atributy** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavit atributy. Budete muset přidat další deklarace identity s názvem **oddělení** a hodnota musí být namapovaný na **user.department**.

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | oddělení| User.Department |

      ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Kliknutím na Přidat atribut chcete otevřít stránku Podrobnosti o atribut přidat atribut oddělení, jak je znázorněno níže-

      ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Klikněte na **Ok** do atribut uložit.

      c. Změňte název atributu **emailaddress** k **e-mailu**.

10. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Přejděte na **nastavení správce LinkedIn** části. Nahrajte soubor XML, který jste právě stáhli z portálu Azure klikněte na možnost soubor nahrát XML.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Klikněte na tlačítko **na** umožňující jednotného přihlašování. Jednotné přihlašování stav se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Vytvoření zkušebního uživatele LinkedIn zvýšení oprávnění

LinkedIn zvýšení oprávnění aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele automaticky se vytvoří v aplikaci. Nastavení na správce stránky na zvýšení oprávnění LinkedIn portálu překlopit přepínač **automaticky přiřadit licence** na aktivní těsně v čase zřizování a to bude také licenci uživateli přiřadit. Zvýšení oprávnění LinkedIn také podporuje zřizování automatické uživatelů, můžete najít další podrobnosti o [sem](active-directory-saas-linkedinelevate-provisioning-tutorial.md) na tom, jak nakonfigurovat uživatele automatické zřizování.

   ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup ke zvýšení oprávnění LinkedIn.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon LinkedIn zvýšení oprávnění, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **zvýšení oprávnění LinkedIn**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LinkedIn zvýšení oprávnění na přístupovém panelu, měli byste obdržet stránku Azure přihlášení a na po úspěšném přihlášení, měli byste obdržet do své aplikace LinkedIn zvýšení oprávnění.

## <a name="additional-resources"></a>Další zdroje informací:

* [Kurz: Konfigurace LinkedIn zvýšení oprávnění pro automatické zřizování s Azure Active Directory uživatelů](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](active-directory-saas-linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png