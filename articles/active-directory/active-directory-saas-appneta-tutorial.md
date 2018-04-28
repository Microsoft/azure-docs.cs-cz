---
title: 'Kurz: Azure Active Directory integrace s nástrojem Sledování výkonu AppNeta | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppNeta sledování výkonu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 6c06b2819516664a367ca31925366eabd05e688e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Kurz: Azure Active Directory integrace s AppNeta sledování výkonu

V tomto kurzu zjistěte, jak integrovat sledování výkonu AppNeta s Azure Active Directory (Azure AD).

Integrace nástroje Sledování výkonu AppNeta s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup ke sledování výkonu AppNeta.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k monitorování výkonu AppNeta (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s nástrojem Sledování výkonu AppNeta, potřebujete následující položky:

- Předplatné služby Azure AD
- Monitorování výkonu AppNeta jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání nástroje Sledování výkonu AppNeta z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Přidání nástroje Sledování výkonu AppNeta z Galerie
Konfigurace integrace nástroje Sledování výkonu AppNeta do Azure AD, potřebujete přidat AppNeta sledování výkonu z Galerie si na seznam spravovaných aplikací SaaS.

**Přidat monitorování výkonu AppNeta z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **sledování výkonu AppNeta**, vyberte **sledování výkonu AppNeta** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Sledování výkonu AppNeta v seznamu výsledků](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí sledování výkonu AppNeta podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v nástroji Sledování výkonu AppNeta je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v nástroji Sledování výkonu AppNeta musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nástrojem Sledování výkonu AppNeta, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření testovacího uživatele monitorování výkonu AppNeta](#create-an-appneta-performance-monitor-test-user)**  – Pokud chcete mít protějšek Britta Simon v nástroji Sledování výkonu AppNeta, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci sledování výkonu AppNeta.

**Ke konfiguraci Azure AD jednotné přihlašování s nástrojem Sledování výkonu AppNeta, proveďte následující kroky:**

1. Na portálu Azure na **sledování výkonu AppNeta** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_samlbase.png)

3. Na **AppNeta výkonu monitorování domény a adresy URL** část, proveďte následující kroky:

    ![AppNeta výkonu monitorování domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.pm.appneta.com`

    b. V **identifikátor** textovému poli, zadejte hodnotu: `PingConnect`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory AppNeta výkonu monitorování klientské](mailto:support@appneta.com) získat tuto hodnotu. 

5. Monitorování výkonu AppNeta aplikace očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-appneta-tutorial/attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
           
    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | FirstName| user.givenname|
    | Příjmení| user.surname|
    | e-mail| user.userprincipalname|
    | jméno| user.userprincipalname|
    | skupiny   | user.assignedroles |
    | Telefon| User.telephoneNumber |
    | název| user.jobtitle|

    > [!NOTE]
    > "skupinami" odkazuje na skupinu zabezpečení v Appneta, která se mapuje na Role ve službě Azure AD. Naleznete [to](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) doc, která vysvětluje, jak vytvořit vlastní role ve službě Azure AD.
        
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Obor názvů nechte prázdné.
    
    e. Klikněte na tlačítko **OK**.  

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-appneta-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **sledování výkonu AppNeta** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory pro sledování výkonu AppNeta](mailto:support@appneta.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-appneta-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-appneta-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-appneta-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-appneta-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Vytvořit testovací uživatele s AppNeta sledování výkonu

Cílem této části je vytvoření uživatele v nástroji Sledování výkonu AppNeta názvem Britta Simon. Sledování výkonu AppNeta podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k AppNeta sledování výkonu, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory pro sledování výkonu AppNeta](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí sledování výkonu AppNeta udělení přístupu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon AppNeta sledování výkonu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **sledování výkonu AppNeta**.

    ![V seznamu aplikací na odkaz AppNeta sledování výkonu](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici monitorování výkonu AppNeta na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci sledování výkonu AppNeta.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_203.png

