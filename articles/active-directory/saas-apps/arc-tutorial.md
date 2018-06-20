---
title: 'Kurz: Azure Active Directory integrace s oblouk publikování - jednotné přihlašování | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oblouk publikování - jednotné přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: bf811d789c0c6effd6f8940ad433092ea9ba04cb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210067"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Kurz: Azure Active Directory integrace s oblouk publikování - jednotného přihlašování

V tomto kurzu zjistěte, jak integrovat oblouk publikování - jednotné přihlašování s Azure Active Directory (Azure AD).

Integrace oblouk publikování - jednotné přihlašování s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k publikování oblouk - jednotné přihlašování.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k publikování oblouk - jednotné přihlašování (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s oblouk publikování - jednotné přihlašování, potřebujete následující položky:

- Předplatné služby Azure AD
- Publikování oblouk - jednotného přihlašování k jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání oblouk publikování - jednotného přihlašování z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Přidání oblouk publikování - jednotného přihlašování z Galerie
Konfigurace integrace oblouk publikování - jednotné přihlašování do služby Azure AD, musíte přidat oblouk publikování - jednotného přihlašování z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat oblouk publikování - jednotného přihlašování z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **oblouk publikování - jednotného přihlašování k**, vyberte **oblouk publikování - jednotného přihlašování k** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Publikování oblouk - jednotné přihlašování v seznamu výsledků](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk publikování – jednotné přihlašování založené na testovacího uživatele názvem "Britta Simon".

Pro jednotné přihlašování pro práci Azure AD je potřeba vědět, jaké příslušného uživatele v oblouk publikování – jednotné přihlašování je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v oblouk publikování - jednotného přihlašování je nutné stanovit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s oblouk publikování - jednotné přihlašování, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit publikování oblouk - jednotného přihlašování k testovací uživatel](#create-an-arc-publishing---sso-test-user)**  – Pokud chcete mít protějšek Britta Simon v oblouk publikování - jednotné přihlašování, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší oblouk publikování - jednotného přihlašování k aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s oblouk publikování - jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure na **oblouk publikování - jednotného přihlašování k** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/arc-tutorial/tutorial_arc_samlbase.png)

3. Na **oblouk publikování - jednotného přihlašování k doméně a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Publikování oblouk - jednotného přihlašování k doméně a adresy URL jeden přihlašování informace](./media/arc-tutorial/tutorial_arc_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Publikování oblouk - jednotného přihlašování k doméně a adresy URL jeden přihlašování informace](./media/arc-tutorial/tutorial_arc_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [oblouk publikování - tým podpory pro jednotné přihlašování klienta](mailto:inf@washpost.com) k získání těchto hodnot. 

5. Publikování oblouk – jednotné přihlašování aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/arc-tutorial/tutorial_arc_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | FirstName | user.givenname |
    | Příjmení | user.surname |
    | e-mail | User.Mail |
    | skupiny | user.assignedroles |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

     ![Konfigurovat jednotné přihlašování](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurovat jednotné přihlašování](./media/arc-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    d. Klikněte na tlačítko **Ok**

    > [!NOTE]
    > Zde **skupiny** atribut je namapována na **user.assignedroles**. Jedná se o vlastní role, které jsou vytvořené v Azure AD, aby mapovala názvy skupiny zpět v aplikaci. Můžete najít další pokyny [sem](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) o tom, jak vytvořit vlastní role v Azure AD. 

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/arc-tutorial/tutorial_arc_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/arc-tutorial/tutorial_general_400.png)
    
9. Na **oblouk publikování - Konfigurace jednotného přihlašování k** klikněte na tlačítko **konfigurace oblouk publikování - jednotného přihlašování k** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Publikování oblouk - Konfigurace jednotného přihlašování](./media/arc-tutorial/tutorial_arc_configure.png) 

10. Konfigurace jednotného přihlašování na **oblouk publikování - jednotného přihlašování k** straně, budete muset odeslat stažené **certifikát (Base64), adresu URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [oblouk Publikování - tým podpory pro jednotné přihlašování](mailto:inf@washpost.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/arc-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/arc-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/arc-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/arc-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Vytvoření oblouk publikování - jednotné přihlašování testovacího uživatele

Cílem této části je vytvoření uživatele volal Britta Simon v oblouk publikování - jednotné přihlašování. Publikování oblouk – jednotného přihlašování podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k publikování oblouk - jednotné přihlašování, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [oblouk publikování - tým podpory pro jednotné přihlašování](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k publikování oblouk - jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon oblouk publikování - jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **oblouk publikování - jednotného přihlašování k**.

    ![Publikování oblouk – odkaz jednotné přihlašování v seznamu aplikací](./media/arc-tutorial/tutorial_arc_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí oblouk publikování - dlaždice jednotného přihlašování na přístupovém panelu jste měli získat automaticky přihlášení k vaší oblouk publikování - jednotného přihlašování k aplikaci.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

