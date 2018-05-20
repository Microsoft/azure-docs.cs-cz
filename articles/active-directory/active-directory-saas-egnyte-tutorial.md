---
title: 'Kurz: Azure Active Directory integrace s Egnyte | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: ea9cb831845a4f99929deac5bc4c63f8c53e5238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Kurz: Azure Active Directory integrace s Egnyte

V tomto kurzu zjistěte, jak integrovat Egnyte s Azure Active Directory (Azure AD).

Integrace Egnyte s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Egnyte
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Egnyte (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Egnyte, potřebujete následující položky:

- Předplatné služby Azure AD
- Egnyte jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Egnyte z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie
Při konfiguraci integrace Egnyte do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Egnyte z galerie.

**Pokud chcete přidat Egnyte z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Egnyte**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. Na panelu výsledků vyberte **Egnyte**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Egnyte podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Egnyte je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Egnyte musí navázat.

V Egnyte, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Egnyte, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele Egnyte](#creating-an-egnyte-test-user)**  – Pokud chcete mít protějšek Britta Simon v Egnyte propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Egnyte.

**Ke konfiguraci Azure AD jednotné přihlašování s Egnyte, proveďte následující kroky:**

1. Na portálu Azure na **Egnyte** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Na **Egnyte domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Egnyte klienta](https://www.egnyte.com/corp/contact_egnyte.html) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. Na **Egnyte konfigurace** klikněte na tlačítko **konfigurace Egnyte** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Egnyte jako správce.

8. Klikněte na tlačítko **nastavení**.
   
   ![Nastavení](./media/active-directory-saas-egnyte-tutorial/ic787819.png "nastavení")

9. V nabídce klikněte na tlačítko **nastavení**.

   ![Nastavení](./media/active-directory-saas-egnyte-tutorial/ic787820.png "nastavení")

10. Klikněte **konfigurace** a pak klikněte **zabezpečení**.

    ![Zabezpečení](./media/active-directory-saas-egnyte-tutorial/ic787821.png "zabezpečení")

11. V **ověření jednotného přihlašování** část, proveďte následující kroky:

    ![Jednotné přihlašování na ověřování](./media/active-directory-saas-egnyte-tutorial/ic787822.png "jednotné přihlašování v ověřování")   
    
    a. Jako **ověření jednotného přihlašování**, vyberte **SAML 2.0**.
   
    b. Jako **zprostředkovatele Identity**, vyberte **AzureAD**.
   
    c. Vložení **SAML jeden přihlašování adresa URL služby** zkopírovaných z portálu Azure do **adresu URL pro přihlášení zprostředkovatele Identity** textové pole.
   
    d. Vložení **SAML Entity ID** který jste zkopírovali z portálu Azure do **ID entity zprostředkovatele Identity** textové pole.
      
    e. Otevření kódovaného certifikátu kódování base-64 v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **certifikát zprostředkovatele Identity** textové pole.
   
    f. Jako **výchozí mapování uživatelů**, vyberte **e-mailová adresa**.
   
    g. Jako **použít hodnotu specifické pro doménu vystavitele**, vyberte **zakázáno**.
   
    h. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-egnyte-test-user"></a>Vytváření testovacího uživatele Egnyte

Pokud chcete povolit uživatelům Azure AD přihlášení k Egnyte, musí být zřízená do Egnyte. V případě Egnyte zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Egnyte** společnosti lokality jako správce.

2. Přejděte na **nastavení \> uživatelé a skupiny**.

3. Klikněte na tlačítko **přidat nové uživatele**a pak vyberte typ uživatele, který chcete přidat.
   
   ![Uživatelé](./media/active-directory-saas-egnyte-tutorial/ic787824.png "uživatelů")

4. V **nové standardní uživatel** část, proveďte následující kroky:
   
   ![Nový uživatel standardní](./media/active-directory-saas-egnyte-tutorial/ic787825.png "nové standardní uživatel")   

   a. Typ **e-mailu**, **uživatelské jméno**a další podrobnosti chcete zřídit platný účet služby Azure Active Directory.
   
   b. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mailové oznámení.
    >

>[!NOTE]
>Můžete použít všechny ostatní Egnyte uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Egnyte zřídit AAD uživatelské účty.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Egnyte.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Egnyte, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Egnyte**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Egnyte na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Egnyte.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

