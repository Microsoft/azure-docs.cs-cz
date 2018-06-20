---
title: 'Kurz: Azure Active Directory integrace s LockPath Keylight | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: d7947a2a294b2232a69a403a765cc2cc29c43405
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218312"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Kurz: Azure Active Directory integrace s LockPath Keylight

V tomto kurzu zjistěte, jak integrovat LockPath Keylight s Azure Active Directory (Azure AD).

Integrace LockPath Keylight s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LockPath Keylight
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LockPath Keylight (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LockPath Keylight, potřebujete následující položky:

- Předplatné služby Azure AD
- LockPath Keylight jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LockPath Keylight z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Přidání LockPath Keylight z Galerie
Při konfiguraci integrace LockPath Keylight do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS LockPath Keylight z galerie.

**Pokud chcete přidat LockPath Keylight z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **LockPath Keylight**.

    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/tutorial_keylight_search.png)

5. Na panelu výsledků vyberte **LockPath Keylight**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testu Azure AD jednotné přihlašování s LockPath Keylight podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v LockPath Keylight je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v LockPath Keylight musí navázat.

V LockPath Keylight přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LockPath Keylight, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  – Pokud chcete mít protějšek Britta Simon v LockPath Keylight propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci LockPath Keylight.

**Ke konfiguraci Azure AD jednotné přihlašování s LockPath Keylight, proveďte následující kroky:**

1. Na portálu Azure na **LockPath Keylight** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

3. Na **LockPath Keylight domény a adresy URL** část, proveďte následující kroky::

    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.keylightgrc.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.keylightgrc.com`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory klienta Keylight LockPath](https://www.lockpath.com/contact/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Raw)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_general_400.png)
    
6. Na **LockPath Keylight konfigurace** klikněte na tlačítko **konfigurace LockPath Keylight** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_keylight_configure.png) 

7. Pokud chcete povolit jednotné přihlašování v LockPath Keylight, proveďte následující kroky:
   
    a. Přihlášení k účtu LockPath Keylight jako správce.
    
    b. V nabídce v horní části, klikněte na tlačítko **osoba**a vyberte **Keylight instalace**.
   
    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/401.png) 

    c. Ve stromovém zobrazení na levé straně, klikněte na tlačítko **SAML**.
   
    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/402.png) 

    d. Na **SAML nastavení** dialogové okno, klikněte na tlačítko **upravit**.
   
    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/404.png) 

8. Na **upravit nastavení SAML** dialogové okno stránky, proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/405.png) 
   
    a. Nastavit **ověřování SAML** k **Active**.

    b. Vložení **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure do **adresu URL pro přihlášení zprostředkovatele Identity** textové pole.

    c. Vložení **jednu adresu URL služby Sign-Out** hodnotu, která jste zkopírovali z portálu Azure do **adresa URL odhlašovací zprostředkovatele Identity** textové pole.

    d. Klikněte na tlačítko **zvolit soubor** vyberte svůj stažený certifikát LockPath Keylight, a pak klikněte na **otevřete** na kterou odešlete certifikát.

    e. Nastavit **Id uživatele SAML umístění** k **NameIdentifier element příkaz subjektu**.
    
    f. Zadejte **poskytovatele služeb Keylight** pomocí následujícího vzorce: **https://&lt;#companyname&gt;. keylightgrc.com**.
    
    g. Nastavit **automatického zřizování uživatelů** k **Active**.

    h. Nastavit **typ účtu automatického zřizování** k **úplné uživatelské**.

    i. Nastavit **role zabezpečení automatického zřizování**, vyberte **standardní uživatel s SAML**.
    
    j. Nastavit **konfigurace zabezpečení automatického zřizování**, vyberte **standardní konfigurace uživatele**.
     
    k. V **atribut e-mailu** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. V **křestní jméno atribut** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. V **poslední atribut name** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/keylight-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Vytvoření zkušebního uživatele LockPath Keylight

V této části vytvoříte volal Britta Simon v LockPath Keylight uživatele. LockPath Keylight podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří při přístupu k LockPath Keylight, pokud uživatel ještě neexistuje. 

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory klienta Keylight LockPath](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu LockPath Keylight.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon LockPath Keylight, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **LockPath Keylight**.

    ![Konfigurovat jednotné přihlašování](./media/keylight-tutorial/tutorial_keylight_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LockPath Keylight na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci LockPath Keylight. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

