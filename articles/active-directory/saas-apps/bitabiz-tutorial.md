---
title: 'Kurz: Azure Active Directory integrace s BitaBIZ | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 12eb02403f9ae7f8c79db07f2ebed2522080649e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901001"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Kurz: Azure Active Directory integrace s BitaBIZ

V tomto kurzu zjistěte, jak integrovat BitaBIZ s Azure Active Directory (Azure AD).

Integrace BitaBIZ s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k BitaBIZ.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BitaBIZ (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BitaBIZ, potřebujete následující položky:

- Předplatné služby Azure AD
- BitaBIZ jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BitaBIZ z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bitabiz-from-the-gallery"></a>Přidání BitaBIZ z Galerie
Při konfiguraci integrace BitaBIZ do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS BitaBIZ z galerie.

**Pokud chcete přidat BitaBIZ z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **BitaBIZ**, vyberte **BitaBIZ** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![BitaBIZ v seznamu výsledků](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s BitaBIZ podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v BitaBIZ je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v BitaBIZ musí navázat.

V BitaBIZ, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BitaBIZ, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele BitaBIZ](#create-a-bitabiz-test-user)**  – Pokud chcete mít protějšek Britta Simon v BitaBIZ propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci BitaBIZ.

**Ke konfiguraci Azure AD jednotné přihlašování s BitaBIZ, proveďte následující kroky:**

1. Na portálu Azure na **BitaBIZ** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. Na **BitaBIZ domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![BitaBIZ domény a adresy URL jednotné přihlašování informace](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Hodnota v výše uvedenou adresu URL je pouze jako ukázka. Aktualizujte hodnotu se skutečným identifikátorem, který je vysvětlen později v tomto kurzu.

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![BitaBIZ domény a adresy URL jednotné přihlašování informace](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://www.bitabiz.com/dashboard`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/bitabiz-tutorial/tutorial_general_400.png)
    
7. Na **BitaBIZ konfigurace** klikněte na tlačítko **konfigurace BitaBIZ** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. V okně prohlížeče jiných webových přihlašování ke klientovi BitaBIZ jako správce.

9. Klikněte na **instalace správce**.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings1.png)

10. Klikněte na **Microsoft integrace** pod **přidejte hodnotu** části.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings2.png)

11. Přejděte dolů v části **Microsoft Azure AD (povolit jednotné přihlašování)** a proveďte následující kroky:

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Zkopírujte hodnotu z **ID Entity ("identifikátor" ve službě Azure AD)** textové pole a vložte ji do **identifikátor** textové pole na **BitaBIZ domény a adresy URL** části na portálu Azure. 
    
    b. V **Azure AD jeden přihlašování adresa URL služby** textovému poli, vložte **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
    
    c. V **ID Entity Azure AD SAML** textovému poli, vložte **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    d. Otevřete váš stažené **Certificate(Base64)** souboru v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **Azure AD certifikát pro podpis (kódováním Base64)** textové pole.

    e. Přidání e-mailové domény obchodní název tedy mycompany.com v **název domény** textbox přiřadit jednotné přihlašování pro uživatele ve vaší společnosti s touto doménou e-mailu (není povinné).
    
    f. Označit **povoleno jednotné přihlašování** BitaBIZ účtu.
    
    g. Klikněte na tlačítko **uložení konfigurace služby Azure AD** uložte a aktivovat Konfigurace jednotného přihlašování.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/bitabiz-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/bitabiz-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/bitabiz-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-bitabiz-test-user"></a>Vytvoření zkušebního uživatele BitaBIZ

Pokud chcete povolit uživatelům Azure AD přihlášení k BitaBIZ, musí být zřízená do BitaBIZ.  
V případě BitaBIZ zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti BitaBIZ jako správce.

2. Klikněte na **instalace správce**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/settings1.png)

3. Klikněte na **přidat uživatele** pod **organizace** části.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user1.png)

4. Klikněte na tlačítko **přidat nové zaměstnance**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user2.png)

5. Na **"Přidat nové zaměstnance"** dialogové okno stránky, proveďte následující kroky:

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user3.png)

    a. V **křestní jméno** textovému poli, název typu první uživatele jako Britta.

    b. V **příjmení** textovému poli, zadejte příjmení uživatele jako Simon.

    c. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

    d. Vybrat datum v **datum zaměstnání**.

    e. Existují další atributy uživatele nepovinné, které se dá nastavit pro uživatele. Naleznete [zaměstnanec instalace Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) další podrobnosti.    
    
    f. Klikněte na tlačítko **uložit zaměstnanec**.
    
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.
    
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu BitaBIZ.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon BitaBIZ, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **BitaBIZ**.

    ![V seznamu aplikací na BitaBIZ odkaz](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici BitaBIZ na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci BitaBIZ.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

