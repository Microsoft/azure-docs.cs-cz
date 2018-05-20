---
title: 'Kurz: Azure Active Directory integrace s pomůže Scout | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pomáhají Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 596a1ba27516755e586d88ab56a20a1daed63fc2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Kurz: Azure Active Directory integrace s pomůže Scout

V tomto kurzu zjistěte, jak integrovat Scout pomoci s Azure Active Directory (Azure AD).

Integrace s Azure AD pomáhají Scout poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k nápovědě Scout.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k pomoci Scout (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s pomůže Scout, potřebujete následující položky:

- Předplatné služby Azure AD
- A pomáhají Scout jednotného přihlašování povolené předplatné

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pomůže Scout z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-help-scout-from-the-gallery"></a>Přidání pomůže Scout z Galerie
Při konfiguraci integrace pomůže Scout do služby Azure AD potřebujete přidat pomůže Scout z Galerie si na seznam spravovaných aplikací SaaS.

**Přidat pomůže Scout z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **pomoci Scout**, vyberte **pomoci Scout** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Nápověda Scout v seznamu výsledků](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s pomůže Scout podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v pomoci Scout je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v nápovědě Scout musí navázat.

Nápovědy Scout používá e-mailové adresy pro přihlášení, takže k navázání vztahu odkaz, použijte stejný **e-mailová adresa** jako **uživatelské jméno** ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s pomůže Scout, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele pomůže Scout](#create-a-help-scout-test-user)**  – Pokud chcete mít protějšek Britta Simon v pomoci Scout, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci pomůže Scout.

**Ke konfiguraci Azure AD jednotné přihlašování s pomůže Scout, proveďte následující kroky:**

1. Na portálu Azure na **pomoci Scout** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Na **pomoci Scout domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Scout domény a adresy URL jeden přihlašování informace nápovědy](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identifikátor** je **"Cílová skupina identifikátor URI (ID Entity poskytovatele služby)"** z pomoci Scout začíná `urn:`

    b. **Adresa URL odpovědi** je **"Po back adresa URL (adresa URL služby příjemce Assertion)"** z pomoci Scout začíná `https://` 

    > [!NOTE] 
    > Hodnoty v těchto adres URL jsou pouze jako ukázka. Je potřeba aktualizovat tyto hodnoty z skutečná adresa URL odpovědi a identifikátor. Získat tyto hodnoty z **jednotné přihlašování** kartě části ověřování, který je vysvětlen později v tomto kurzu.

4. Pokud chcete nakonfigurovat aplikace **SP** initiated režimu, zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok:

    ![Scout domény a adresy URL jeden přihlašování informace nápovědy](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://secure.helpscout.net/members/login/`
     
5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. Na **pomoci konfigurace Scout** klikněte na tlačítko **konfigurace pomoci Scout** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti pomůže Scout jako správce.

9. Jakmile jste přihlášeni v klikněte na položku **"Manage"** z horní nabídce a potom vyberte **"Společnost"** z rozevírací nabídky.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Vyberte **"Ověřování"** z nabídky na levé straně. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Tím přejdete do části SAML nastavení a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Kopírování **po zpět adresy URL (adresa URL služby příjemce Assertion)** a vložte hodnotu v **adresa URL odpovědi** pole na portálu Azure v rámci pomoci Scout **domény a adresy URL** části.
    
    b. Kopírování **identifikátor URI cílové skupiny (ID Entity poskytovatele služby)** a vložte hodnotu v **identifikátor** pole na portálu Azure v rámci pomoci Scout **domény a adresy URL** části.

12. Přepnutí **povolit SAML** na a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. V **adresy jednotného přihlašování** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
    
    b. Klikněte na tlačítko **nahrát certifikát** nahrát **Certificate(Base64)** stáhli z portálu Azure.

    c. Zadejte vaší organizaci e-mailu doménách e.x. - `contoso.com` v **e-mailových domén** textové pole. Více domén můžete oddělit čárkou. Kdykoli pomůže Scout uživatel nebo správce, který zadá konkrétní domény na [protokolu stránku nápovědy Scout](https://secure.helpscout.net/members/login/) poskytovateli Identity k ověření pomocí svých přihlašovacích údajů, budou směrovány.

    d. Nakonec můžete přepínat **přihlašování Force SAML** Pokud chcete uživatelům přihlašovat pouze k pomoci Scout prostřednictvím prostřednictvím této metody. Pokud stále chcete ponechte možnost se přihlásit pomocí svých přihlašovacích údajů pomůže Scout, můžete nechat se přepne do vypnuté polohy. I když je tato možnost povolena, vlastníka účtu vždy bude moct přihlásit k pomoci Scout s své heslo účtu.

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-help-scout-test-user"></a>Vytvoření zkušebního uživatele pomůže Scout

Cílem této části je vytvoření uživatele volal Britta Simon v nápovědě Scout. Nápověda Scout podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v nápovědě Scout ještě neexistuje, vytvoří se nový při pokusu o přístup k nápovědě Scout.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu pomáhají Scout.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon pomůže Scout, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **pomoci Scout**.

    ![Na odkaz Nápověda Scout v seznamu aplikací](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici pomůže Scout na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci pomůže Scout.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

