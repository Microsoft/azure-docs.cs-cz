---
title: 'Kurz: Azure Active Directory integrace s Vidyard | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 331eef62f41d3f8f0d5d9f4badaea44c82293b33
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654771"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Kurz: Azure Active Directory integrace s Vidyard

V tomto kurzu zjistěte, jak integrovat Vidyard s Azure Active Directory (Azure AD).

Integrace Vidyard s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Vidyard.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Vidyard (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Vidyard, potřebujete následující položky:

- Předplatné služby Azure AD
- Vidyard jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Vidyard z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-vidyard-from-the-gallery"></a>Přidání Vidyard z Galerie
Při konfiguraci integrace Vidyard do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Vidyard z galerie.

**Pokud chcete přidat Vidyard z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Vidyard**, vyberte **Vidyard** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Vidyard v seznamu výsledků](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vidyard podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Vidyard je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Vidyard musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vidyard, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Vidyard](#create-a-vidyard-test-user)**  – Pokud chcete mít protějšek Britta Simon v Vidyard propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Vidyard.

**Ke konfiguraci Azure AD jednotné přihlašování s Vidyard, proveďte následující kroky:**

1. Na portálu Azure na **Vidyard** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_samlbase.png)

3. Na **Vidyard domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Vidyard domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_url2.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Vidyard domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty budou aktualizovat se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu

1. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-vidyard-tutorial/tutorial_general_400.png)

7. Na **Vidyard konfigurace** klikněte na tlačítko **konfigurace Vidyard** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_configure.png)

8. V okně prohlížeče jiný web Přihlaste se na váš web společnosti Vidyard softwaru jako správce.

9. Na řídicím panelu Vidyard vyberte **skupiny** > **zabezpečení**

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure1.png)

1. Klikněte na tlačítko **nový profil** kartě.

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure2.png)

11. V **konfigurace SAML** část, proveďte následující kroky:

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure3.png)

    a. Zadejte název profilu Obecné v **název profilu** textové pole.

    b. Kopírování **jednotné přihlašování uživatele přihlašovací stránku** a vložte ji do **přihlásit na adrese URL** textového pole v **Vidyard domény a adresy URL části** na portálu Azure.

    c. Kopírování **adresa URL služby ACS** a vložte ji do **adresa URL odpovědi** textového pole v **Vidyard domény a adresy URL části** na portálu Azure.

    d. Kopírování **URL vystavitele nebo Metadata** a vložte ji do **identifikátor** textového pole v **Vidyard domény a adresy URL části** na portálu Azure.

    e. Z portálu Azure v poznámkovém bloku otevřete váš soubor stažený certifikát a vložte jej do **certifikát X.509** textové pole.

    f. V **adresu URL koncového bodu SAML** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** zkopírovaných z portálu Azure.

    g. Klikněte na tlačítko **potvrďte**.

12. Na kartě jednotné přihlašování vyberte **přiřadit** vedle stávající profil

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Po vytvoření profilem jednotné přihlašování, přiřaďte ho pro všechny skupiny, pro které uživatelé budou potřebovat přístup přes Azure. Pokud uživatel neexistuje v rámci skupiny, do které byly přiřazeny, Vidyard automaticky vytvořte účet uživatele a přiřaďte jejich role v reálném čase.

13. Vyberte skupinu vaší organizace, která se zobrazí na **skupiny k dispozici pro přiřazení**.

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure5.png)

14. Přiřazené skupiny v části můžete zobrazit **momentálně přiřazené skupiny**. Vyberte roli pro skupinu podle vaší organizace a klikněte na **potvrdit**.

    ![Konfigurace Vidyard](./media/active-directory-saas-vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Další informace najdete v části [tento doc](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-vidyard-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-vidyard-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-vidyard-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-vidyard-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-vidyard-test-user"></a>Vytvoření zkušebního uživatele Vidyard

Cílem této části je vytvoření uživatele v Vidyard nazývá Britta Simon. Vidyard podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Vidyard, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Vidyard.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Vidyard, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Vidyard**.

    ![V seznamu aplikací na Vidyard odkaz](./media/active-directory-saas-vidyard-tutorial/tutorial_vidyard_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Vidyard na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Vidyard.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vidyard-tutorial/tutorial_general_203.png

