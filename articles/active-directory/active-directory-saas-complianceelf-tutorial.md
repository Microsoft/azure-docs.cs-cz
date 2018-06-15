---
title: 'Kurz: Azure Active Directory integrace s ELF dodržování předpisů | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ELF dodržování předpisů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 66ee9f82d202537849491acda01e3e55ee7968ed
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343398"
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Kurz: Azure Active Directory integrace s ELF dodržování předpisů

V tomto kurzu zjistěte, jak integrovat ELF dodržování předpisů s Azure Active Directory (Azure AD).

Integrace ELF dodržování předpisů s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k ELF dodržování předpisů.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ELF dodržování předpisů (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ELF dodržování předpisů, potřebujete následující položky:

- Předplatné služby Azure AD
- Dodržování předpisů ELF jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ELF dodržování předpisů z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-compliance-elf-from-the-gallery"></a>Přidání ELF dodržování předpisů z Galerie
Při konfiguraci integrace ELF dodržování předpisů do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ELF dodržování předpisů z galerie.

**Pokud chcete přidat ELF dodržování předpisů z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **dodržování předpisů ELF**, vyberte **dodržování předpisů ELF** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Dodržování předpisů ELF v seznamu výsledků](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ELF dodržování předpisů podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ELF dodržování předpisů je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ELF dodržování předpisů je nutné stanovit.

V dodržování předpisů ELF přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ELF dodržování předpisů, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele dodržování předpisů ELF](#create-a-compliance-elf-test-user)**  – Pokud chcete mít protějšek Britta Simon v ELF dodržování předpisů, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ELF dodržování předpisů.

**Ke konfiguraci Azure AD jednotné přihlašování s ELF dodržování předpisů, proveďte následující kroky:**

1. Na portálu Azure na **dodržování předpisů ELF** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. Na **domény ELF dodržování předpisů a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Dodržování předpisů ELF domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL jako: `https://sso.cordium.com`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Dodržování předpisů ELF domény a adresy URL jednotného přihlašování](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.complianceelf.com`
    
    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tento hodnoty s skutečná adresa URL přihlašování. Obraťte se na [tým podpory dodržování předpisů ELF](mailto:support@complianceelf.com) získat tuto hodnotu.

5. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-complianceelf-tutorial/tutorial_metadataurl.png)
     
6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-complianceelf-tutorial/tutorial_general_400.png)

7. Konfigurace jednotného přihlašování na **ELF dodržování předpisů** straně, budete muset odeslat **adresu Url aplikace federační Metadata** k [tým podpory dodržování předpisů ELF](mailto:support@complianceelf.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-compliance-elf-test-user"></a>Vytvoření zkušebního uživatele ELF dodržování předpisů

V této části vytvoříte uživatele volal Britta Simon v ELF dodržování předpisů. Práce s [tým podpory dodržování předpisů ELF](mailto:support@complianceelf.com) přidat uživatele do platformy ELF dodržování předpisů. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ELF dodržování předpisů.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ELF dodržování předpisů, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **dodržování předpisů ELF**.

    ![V seznamu aplikací na odkaz ELF dodržování předpisů](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ELF dodržování předpisů na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ELF dodržování předpisů.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_203.png

