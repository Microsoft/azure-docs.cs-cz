---
title: 'Kurz: Integrace Azure Active Directory se řídí Data3Sixty Infogix | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a regulovat Data3Sixty Infogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3acdfbc7665bec6cfb8821f33e642f29eec84be1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214912"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Kurz: Integrace Azure Active Directory se řídí Data3Sixty Infogix

V tomto kurzu zjistěte, jak integrovat řídí Data3Sixty Infogix s Azure Active Directory (Azure AD).

Integrace Infogix Data3Sixty řídí s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Infogix Data3Sixty řídí.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Infogix Data3Sixty řídí (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Infogix Data3Sixty řídí, potřebujete následující položky:

- Předplatné služby Azure AD
- Infogix Data3Sixty řídí jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Infogix Data3Sixty řídí z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Přidání Infogix Data3Sixty řídí z Galerie
Chcete-li nakonfigurovat integraci Infogix Data3Sixty řídí do služby Azure AD, přidejte Infogix Data3Sixty řídí z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Infogix Data3Sixty řídí z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Infogix Data3Sixty řídí**, vyberte **Infogix Data3Sixty řídí** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Infogix Data3Sixty řídí v seznamu výsledků](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Infogix Data3Sixty řídí podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Infogix Data3Sixty řídí je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Infogix Data3Sixty řídí musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Infogix Data3Sixty řídit, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele řídí Data3Sixty Infogix](#create-an-infogix-data3sixty-govern-test-user)**  – Pokud chcete mít protějšek Britta Simon v Infogix Data3Sixty řídí propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Infogix Data3Sixty řídí.

**Ke konfiguraci Azure AD jednotné přihlašování s Infogix Data3Sixty řídí, proveďte následující kroky:**

1. Na portálu Azure na **Infogix Data3Sixty řídí** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

3. Na **Infogix Data3Sixty řídí domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Infogix Data3Sixty řídí domény a adresy URL jednotné přihlašování informace](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://data3sixty.com/ui`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.data3sixty.com/sso/acs`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Infogix Data3Sixty řídí domény a adresy URL jednotné přihlašování informace](./media/infogix-tutorial/tutorial_infogix_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Infogix Data3Sixty řídí klienta](mailto:data3sixtysupport@infogix.com) k získání těchto hodnot.

5. Řídí Data3Sixty Infogix aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování attb](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | FirstName           | user.givenname |
    | Příjmení        | user.surname |
    | uživatelské jméno       | User.Mail    |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Konfigurace Addattb přihlášení](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Raw)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/infogix-tutorial/tutorial_infogix_certificate.png)

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/infogix-tutorial/tutorial_general_400.png)
    
9. Na **Infogix Data3Sixty řídí konfigurace** klikněte na tlačítko **konfigurace řídí Data3Sixty Infogix** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Infogix Data3Sixty řídí konfigurace](./media/infogix-tutorial/tutorial_infogix_configure.png) 

10. Konfigurace jednotného přihlašování na **Infogix Data3Sixty řídí** straně, budete muset odeslat stažené **certifikát (Raw), adresu URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [ Řídí Data3Sixty Infogix tým podpory](mailto:data3sixtysupport@infogix.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/infogix-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/infogix-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/infogix-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/infogix-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Vytvoření zkušebního uživatele řídí Data3Sixty Infogix


Cílem této části je vytvoření uživatele volal Britta Simon v Infogix Data3Sixty řídí. Řídí Data3Sixty Infogix podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Infogix Data3Sixty řídí, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [Infogix Data3Sixty řídí tým podpory](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v Infogix Data3Sixty řídí udělení přístupu.

![Přiřadit role uživatele][200] 

**Chcete-li řídit Data3Sixty Infogix přiřadit Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Infogix Data3Sixty řídí**.

    ![Řídí Data3Sixty Infogix odkaz v seznamu aplikací](./media/infogix-tutorial/tutorial_infogix_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici řídí Data3Sixty Infogix na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Infogix Data3Sixty řídí.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

