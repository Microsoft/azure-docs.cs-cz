---
title: 'Kurz: Azure Active Directory integrace s Jitbit Helpdesk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jitbit technickou podporu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: c8e387bb98ad2e23c667ba058ff8ab5dbfedffbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343925"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Azure Active Directory integrace s Jitbit Helpdesk

V tomto kurzu zjistěte, jak integrovat Jitbit pracovník s Azure Active Directory (Azure AD).

Integrace Jitbit pracovník s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup na technickou podporu Jitbit
- Můžete povolit uživatelům, aby automaticky získat přihlášeného na technickou podporu Jitbit (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jitbit Helpdesk, potřebujete následující položky:

- Předplatné služby Azure AD
- Jitbit Helpdesk jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jitbit Helpdesk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Přidání Jitbit Helpdesk z Galerie
Při konfiguraci integrace Jitbit Helpdesk do služby Azure AD potřebujete přidat Jitbit Helpdesk z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Jitbit Helpdesk z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Jitbit Helpdesk**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. Na panelu výsledků vyberte **Jitbit Helpdesk**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Jitbit Helpdesk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Jitbit Helpdesk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Jitbit Helpdesk je potřeba vytvořit.

V Jitbit Helpdesk přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jitbit Helpdesk, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v Jitbit Helpdesk, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Jitbit technickou podporu.

**Ke konfiguraci Azure AD jednotné přihlašování s Jitbit Helpdesk, proveďte následující kroky:**

1. Na portálu Azure na **Jitbit Helpdesk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. Na **Jitbit Helpdesk domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Jitbit Helpdesk klienta](https://www.jitbit.com/support/) získat tuto hodnotu. 
    
    b.  V **identifikátor** textovému poli, zadejte adresu URL jako následující: `https://www.jitbit.com/web-helpdesk/`

    
 


4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. Na **Jitbit Helpdesk konfigurace** klikněte na tlačítko **konfigurace Jitbit Helpdesk** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Jitbit Helpdesk.

8. Na panelu nástrojů v horní části klikněte na tlačítko **správy**.
   
    ![Správa](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "správy")

9. Klikněte na tlačítko **obecné nastavení**.
   
    ![Uživatelé, společností a oprávnění](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "uživatelů, společností a oprávnění")

10. V **nastavení ověřování** konfigurace části, proveďte následující kroky:
   
    ![Nastavení ověřování](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "nastavení ověřování")
    
    a. Vyberte **povolit SAML 2.0 jeden přihlásit**, se přihlásit pomocí jednotné přihlašování (SSO), s **OneLogin**.

    b. V **adresu URL koncového bodu** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. Otevřete váš **kódování base-64** kódování certifikátu v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textové pole

    d. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, název typu jako **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Vytvoření zkušebního uživatele Jitbit Helpdesk

Pokud chcete povolit uživatelům Azure AD přihlášení do Jitbit Helpdesk, musí být zřízená do Jitbit technickou podporu.  V případě Jitbit Helpdesk zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Jitbit Helpdesk** klienta.

2. V nabídce v horní části, klikněte na tlačítko **správy**.
   
    ![Správa](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "správy")

3. Klikněte na tlačítko **uživatelů, společností a oprávnění**.
   
    ![Uživatelé, společností a oprávnění](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "uživatelů, společností a oprávnění")

4. Klikněte na tlačítko **přidat uživatele**.
   
    ![Přidat uživatele](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "přidat uživatele")
   
5. V části Vytvoření typů data účtu Azure AD, které chcete zřídit následujícím způsobem:

    ![Vytvoření](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "vytvořit")
   
   a. V **uživatelské jméno** textovému poli, typ **BrittaSimon**, uživatelské jméno jako v portálu Azure.

   b. V **e-mailu** jako typ e-mail uživatele k textovému poli, **BrittaSimon@contoso.com**.

   c. V **křestní jméno** textovému poli, zadejte jméno uživatele, jako je **Britta**.

   d. V **příjmení** textovému poli, zadejte příjmení uživatele jako **Simon**.
   
   e. Klikněte na možnost **Vytvořit**.

>[!NOTE]
>Další nástroje pro tvorbu účet uživatele Jitbit Helpdesk nebo rozhraní API poskytovaných Jitbit Helpdesk můžete použít ke zřízení uživatelských účtů Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu na Jitbit technickou podporu.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon na technickou podporu Jitbit, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Jitbit Helpdesk**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Jitbit technickou podporu na přístupovém panelu, měli byste obdržet přihlašovací stránku aplikace Jitbit technickou podporu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

