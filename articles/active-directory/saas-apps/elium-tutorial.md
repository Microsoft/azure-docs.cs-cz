---
title: 'Kurz: Azure Active Directory integrace s Elium | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 1ce186978d910cfdae068e68b5f86a8ebd5fa712
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227417"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Kurz: Azure Active Directory integrace s Elium

V tomto kurzu zjistěte, jak integrovat Elium s Azure Active Directory (Azure AD).

Integrace Elium s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Elium.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Elium (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Elium, potřebujete následující položky:

- Předplatné služby Azure AD
- Elium jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Elium z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-elium-from-the-gallery"></a>Přidání Elium z Galerie
Při konfiguraci integrace Elium do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Elium z galerie.

**Pokud chcete přidat Elium z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Elium**, vyberte **Elium** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Elium v seznamu výsledků](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Elium podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Elium je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Elium musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Elium, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Elium](#create-an-elium-test-user)**  – Pokud chcete mít protějšek Britta Simon v Elium propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Elium.

**Ke konfiguraci Azure AD jednotné přihlašování s Elium, proveďte následující kroky:**

1. Na portálu Azure na **Elium** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/elium-tutorial/tutorial_elium_samlbase.png)

3. Na **Elium domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Elium domény a adresy URL jednotné přihlašování informace](./media/elium-tutorial/tutorial_elium_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Elium domény a adresy URL jednotné přihlašování informace](./media/elium-tutorial/tutorial_elium_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Zobrazí se tyto hodnoty z **soubor metadat SP** ke stažení na `https://<platform-domain>.elium.com/login/saml2/metadata`, který se vysvětluje dále v tomto kurzu.

5. Aplikace Elium očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/tutorial_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
           
    | Název atributu | Hodnota atributu |   
    | ---------------| ----------------|
    | e-mail   |User.Mail |
    | křestní_jméno| user.givenname |
    | Příjmení| user.surname|
    | funkce| user.jobtitle|
    | Společnosti| User.CompanyName|
    
    > [!NOTE]
    > Toto jsou výchozí deklarace identity. **Deklarace identity e-mailu je vyžadována pouze**. JIT zřizování také pouze e-mailu je povinné deklarace identity. Další vlastní deklarace identity se může lišit od jednoho zákazníka platformy na jiné platformě zákazníka.

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/tutorial_attribute_04.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Obor názvů nechte prázdné.
    
    e. Klikněte na tlačítko **OK**. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/elium-tutorial/tutorial_elium_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/elium-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Elium jako správce.

8. Klikněte na **profil uživatele** z pravém horním rohu a pak vyberte **správy**.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/user1.png)

9. Vyberte **zabezpečení** kartě.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/user2.png)

10. Přejděte dolů k položce **jednotné přihlašování (SSO)** části a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/user3.png)

    a. Zkopírujte hodnotu **ověřte, že ověřování typu SAML2 funguje pro váš účet** a vložte jej do **přihlašovací adresa URL** textové pole na **Elium domény a adresy URL** části ve službě Azure portál.

    > [!NOTE]
    > Po dokončení konfigurace jednotného přihlašování, můžete vždy přístup k výchozí stránka vzdálené přihlášení na následující adrese URL: `https://<platform_domain>/login/regular/login` 

    b. Vyberte **federation povolit typu SAML2** zaškrtávací políčko.

    c. Vyberte **zřizování JIT** zaškrtávací políčko.

    d. Otevřete **SP Metadata** kliknutím na **Stáhnout** tlačítko.

    e. Vyhledejte **entityID** v **SP Metadata** souboru, zkopírujte **entityID** hodnotu a vložte jej do **identifikátor** textové pole na  **Elium domény a adresy URL** části portálu Azure. 

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/user4.png)

    f. Vyhledejte **AssertionConsumerService** v **SP Metadata** souboru, zkopírujte **umístění** hodnotu a vložte jej do **adresa URL odpovědi** textové pole na **Elium domény a adresy URL** části portálu Azure.

    ![Konfigurovat jednotné přihlašování](./media/elium-tutorial/user5.png)

    g. Otevřete soubor metadat stažený z portálu Azure do programu Poznámkový blok, zkopírujte obsah a vložte ji do **IdP Metadata** textové pole.

    h. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/elium-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/elium-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/elium-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/elium-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-elium-test-user"></a>Vytvořit uživatele s Elium testu

Cílem této části je vytvoření uživatele v Elium nazývá Britta Simon. Elium podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Elium, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Elium.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Elium, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Elium**.

    ![V seznamu aplikací na Elium odkaz](./media/elium-tutorial/tutorial_elium_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Elium na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Elium.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

