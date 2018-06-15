---
title: 'Kurz: Azure Active Directory integrace s Moxtra | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 1d0b89cf0d090795a305b5bfcce070136cf0289a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352034"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Kurz: Azure Active Directory integrace s Moxtra

V tomto kurzu zjistěte, jak integrovat Moxtra s Azure Active Directory (Azure AD).

Integrace Moxtra s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Moxtra
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Moxtra (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Moxtra, potřebujete následující položky:

- Předplatné služby Azure AD
- Moxtra jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Moxtra z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moxtra-from-the-gallery"></a>Přidání Moxtra z Galerie
Při konfiguraci integrace Moxtra do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Moxtra z galerie.

**Pokud chcete přidat Moxtra z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Moxtra**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. Na panelu výsledků vyberte **Moxtra**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Moxtra podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Moxtra je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Moxtra musí navázat.

V Moxtra, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Moxtra, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Moxtra](#creating-a-moxtra-test-user)**  – Pokud chcete mít protějšek Britta Simon v Moxtra propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Moxtra.

**Ke konfiguraci Azure AD jednotné přihlašování s Moxtra, proveďte následující kroky:**

1. Na portálu Azure na **Moxtra** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. Na **Moxtra domény a adresy URL** část, proveďte následující krok:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://www.moxtra.com/service/#login`

4. Aplikace Moxtra očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tuto konfiguraci. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | FirstName | user.givenname |
    | Příjmení | user.surname |
    | idpid    | < SAML Entity ID > 

    > [!Note]
    > Hodnota **idpid** atribut není skutečné. Můžete získat skutečná hodnota z **Stručná referenční příručka** oddílu pod **Moxtra konfigurace**.
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Klikněte na tlačítko **OK**.
    
5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. Na **Moxtra konfigurace** klikněte na tlačítko **konfigurace Moxtra** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti Moxtra jako správce.

9. Na panelu nástrojů na levé straně klikněte na tlačítko **konzoly pro správu > SAML jednotné přihlašování**a potom klikněte na **nový**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. Na **SAML** proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. V **název** textovému poli, zadejte název pro svou konfiguraci (například: *SAML*). 
  
    b. V **IdP Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure. 
 
    c. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure. 
 
    d. V **AuthnContextClassRef** textovému poli, typ **urn: oasis: názvy: tc: SAML:2.0:ac:classes:Password**. 
 
    e. V **NameID formátu** textovému poli, typ **urn: oasis: názvy: tc: SAML:1.1:nameid-formátu: emailAddress**. 
 
    f. Otevřete certifikát, který jste si stáhli z portálu Azure v poznámkovém bloku kopírovat obsah a vložte ji do **certifikát** textové pole.    
 
    g. Do textového pole SAML e-mailové domény zadejte e-mailovou doménu SAML.    
  
    >[!NOTE]
    >Postup ověření domény zobrazíte kliknutím "**i**" níže.

    h. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-moxtra-test-user"></a>Vytvoření zkušebního uživatele Moxtra

Cílem této části je vytvoření uživatele v Moxtra nazývá Britta Simon.

**Vytvoření uživatele v Moxtra nazývá Britta Simon, proveďte následující kroky:**

1. Přihlásit se k serveru vaší společnosti Moxtra jako správce.

2. Na panelu nástrojů na levé straně klikněte na tlačítko **konzoly pro správu > Správa uživatelů**a potom **přidat uživatele**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. Na **přidat uživatele** dialogové okno, proveďte následující kroky:
  
    a. V **křestní jméno** textovému poli, typ **Britta**.
  
    b. V **příjmení** textovému poli, typ **Simon**.
  
    c. V **e-mailu** textovému poli, typ Britta na e-mailová adresa, aby se pro stejné jako na portálu Azure.
  
    d. V **dělení** textovému poli, typ **Dev**.
  
    e. V **oddělení** textovému poli, typ **IT**.
  
    f. Vyberte **správce**.
  
    g. Klikněte na tlačítko **Add** (Přidat).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Moxtra.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Moxtra, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Moxtra**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Moxtra na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Moxtra.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

