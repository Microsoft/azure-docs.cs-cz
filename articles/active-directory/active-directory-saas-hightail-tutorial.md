---
title: 'Kurz: Azure Active Directory integrace s Hightail | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 2333a56cd9369cb66c4944304472462d2f2f7511
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Kurz: Azure Active Directory integrace s Hightail

V tomto kurzu zjistěte, jak integrovat Hightail s Azure Active Directory (Azure AD).

Integrace Hightail s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Hightail
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Hightail (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Hightail, potřebujete následující položky:

- Předplatné služby Azure AD
- Hightail jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Hightail z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hightail-from-the-gallery"></a>Přidání Hightail z Galerie
Při konfiguraci integrace Hightail do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Hightail z galerie.

**Pokud chcete přidat Hightail z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Hightail**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_search.png)

5. Na panelu výsledků vyberte **Hightail**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Hightail podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Hightail je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Hightail musí navázat.

V Hightail, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Hightail, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Hightail](#creating-a-hightail-test-user)**  – Pokud chcete mít protějšek Britta Simon v Hightail propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Hightail.

**Ke konfiguraci Azure AD jednotné přihlašování s Hightail, proveďte následující kroky:**

1. Na portálu Azure na **Hightail** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_samlbase.png)

3. Na **Hightail domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url.png)

     V **adresa URL odpovědi** textovému poli, zadejte adresu URL jako: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE] 
    > Předchozí hodnota není skutečné hodnoty. Hodnota bude aktualizován skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu.
 
4. Na **Hightail domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url1.png)

    a. Klikněte **zobrazit upřesňující nastavení adresy URL**.

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://www.hightail.com/loginSSO`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_certificate.png) 

5. Hightail aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **"Atrribute"** aplikace. Následující snímek obrazovky ukazuje příklad pro tento. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_attribute.png) 

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | Identity uživatele | user.mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_general_400.png)

8. Na **Hightail konfigurace** klikněte na tlačítko **konfigurace Hightail** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Před konfigurací jednotné přihlašování v aplikaci Hightail, prosím seznamu povolených vaše e-mailovou doménu s Hightail týmu, aby všichni uživatelé, kteří používají tuto doménu můžete použít funkci jednotného přihlašování.


9. Pokud chcete získat jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, musíte k přihlašování ke klientovi Hightail jako správce.
   
    a. V nabídce v horní části, klikněte **účet** a vyberte **konfigurace SAML**.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. Zaškrtněte políčko z **povolit ověřování SAML**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Otevření kódovaného certifikátu kódování base-64 v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **SAML podpisový certifikát tokenů** textové pole.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 

    d. V **autority SAML (zprostředkovatele Identity)** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** zkopírovaných z portálu Azure.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Pokud chcete nakonfigurovat aplikace **IDP iniciované režimu** vyberte **"Zprostředkovatele Identity (IdP) iniciované přihlášení"**. Pokud **SP iniciované režimu** vyberte **"Přihlášení iniciované poskytovatelem služeb (SP)"**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Zkopírujte adresu URL příjemce SAML pro vaše instance a vložte jej do **adresa URL odpovědi** textového pole v **Hightail domény a adresy URL** části na portálu Azure.
    
    g. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-hightail-test-user"></a>Vytvoření zkušebního uživatele Hightail

Cílem této části je vytvoření uživatele v Hightail nazývá Britta Simon. 

Neexistuje žádná položka akce pro vás v této části. Hightail podporuje zřizování uživatelů za běhu v závislosti na vlastní deklarace. Pokud jste nakonfigurovali vlastní deklarace identity, jak je uvedeno v části **[konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** vyšší, se automaticky vytvoří uživatele v aplikaci ještě neexistuje. 

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Hightail.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Hightail, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Hightail**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Hightail na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Hightail.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png

