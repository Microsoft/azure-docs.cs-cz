---
title: 'Kurz: Azure Active Directory integrace s Promapp | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 729644e350e3c0ad4a5ea695d24ddaf4164952fc
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213973"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Kurz: Azure Active Directory integrace s Promapp

V tomto kurzu zjistěte, jak integrovat Promapp s Azure Active Directory (Azure AD).

Integrace Promapp s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Promapp
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Promapp (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Promapp, potřebujete následující položky:

- Předplatné služby Azure AD
- Promapp jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Promapp z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-promapp-from-the-gallery"></a>Přidání Promapp z Galerie
Při konfiguraci integrace Promapp do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Promapp z galerie.

**Pokud chcete přidat Promapp z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Promapp**.

    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/tutorial_promapp_search.png)

5. Na panelu výsledků vyberte **Promapp**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Promapp podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Promapp je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Promapp musí navázat.

V Promapp, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Promapp, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Promapp](#creating-a-promapp-test-user)**  – Pokud chcete mít protějšek Britta Simon v Promapp propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Promapp.

**Ke konfiguraci Azure AD jednotné přihlašování s Promapp, proveďte následující kroky:**

1. Na portálu Azure na **Promapp** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_samlbase.png)

3. Na **Promapp domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > V současné době integrace Azure AD s Promapp pouze byla nakonfigurována pro ověřování služby initiated například přejdete na adresu URL Promapp zahájí proces ověřování. Ale adresa URL odpovědi je povinné pole.
    
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Promapp klienta](https://www.promapp.com/about-us/contact-us/) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_general_400.png)

7. Na **Promapp konfigurace** klikněte na tlačítko **konfigurace Promapp** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_configure.png) 

8. Přihlašování k webu společnosti Promapp jako správce. 

9. V nabídce v horní části, klikněte na tlačítko **správce**. 
   
    ![Azure AD jednotné přihlášení][12]

10. Klikněte na **Konfigurovat**. 
   
    ![Azure AD jednotné přihlášení][13]

11. Na **zabezpečení** dialogové okno, proveďte následující kroky:
   
    ![Azure AD jednotné přihlášení][14]
    
    a. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **adresu URL pro přihlášení SSO** textové pole.
    
    b. Jako **jednotné přihlašování – jednotné přihlašování v režimu**, vyberte **volitelné**a potom klikněte na **Uložit**.

    > [!NOTE]
    > **Volitelné** režim je určené jenom pro testování. Jakmile budete spokojeni s konfigurací, vyberte **požadované** režimu vynutit všichni uživatelé k ověření pomocí služby Azure AD.

    c. Otevřete zkopírujte stažený certifikát v poznámkovém bloku obsahu certifikátu bez první řádek (---**začít certifikát**---) a poslední řádek (---**END CERTIFICATE**---), vložte jej do  **Certifikát x.509 jednotného přihlašování k** textovému poli a potom klikněte na **Uložit**.
        
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/promapp-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-promapp-test-user"></a>Vytvoření zkušebního uživatele Promapp

Promapp aplikace podporuje pouze za běhu zřizování. To znamená, uživatelský účet se automaticky vytvoří v případě potřeby při pokusu o přístup k aplikaci pomocí přístupového panelu.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Promapp.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Promapp, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Promapp**.

    ![Konfigurovat jednotné přihlašování](./media/promapp-tutorial/tutorial_promapp_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

K testování aplikace v **SP** iniciované režimu, budete muset spustit ověřování z vaší lokality Promapp. To můžete provést kliknutím na tlačítko "přihlášení pomocí jednotného přihlašování, na přihlašovací stránku a přitom **volitelné** je povolený režim.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promapp-tutorial/tutorial_general_01.png
[2]: ./media/promapp-tutorial/tutorial_general_02.png
[3]: ./media/promapp-tutorial/tutorial_general_03.png
[4]: ./media/promapp-tutorial/tutorial_general_04.png
[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/promapp-tutorial/tutorial_general_100.png

[200]: ./media/promapp-tutorial/tutorial_general_200.png
[201]: ./media/promapp-tutorial/tutorial_general_201.png
[202]: ./media/promapp-tutorial/tutorial_general_202.png
[203]: ./media/promapp-tutorial/tutorial_general_203.png

