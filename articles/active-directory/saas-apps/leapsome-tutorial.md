---
title: 'Kurz: Azure Active Directory integrace s Leapsome | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: b23a93db7912aa25b420157241c41533f4f48a27
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938377"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Kurz: Azure Active Directory integrace s Leapsome

V tomto kurzu zjistěte, jak integrovat Leapsome s Azure Active Directory (Azure AD).

Integrace Leapsome s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Leapsome.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Leapsome (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Leapsome, potřebujete následující položky:

- Předplatné služby Azure AD
- Leapsome jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Leapsome z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-leapsome-from-the-gallery"></a>Přidání Leapsome z Galerie
Při konfiguraci integrace Leapsome do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Leapsome z galerie.

**Pokud chcete přidat Leapsome z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Leapsome**, vyberte **Leapsome** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Leapsome v seznamu výsledků](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Leapsome podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Leapsome je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Leapsome musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Leapsome, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Leapsome](#create-a-leapsome-test-user)**  – Pokud chcete mít protějšek Britta Simon v Leapsome propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Leapsome.

**Ke konfiguraci Azure AD jednotné přihlašování s Leapsome, proveďte následující kroky:**

1. Na portálu Azure na **Leapsome** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

3. Na **Leapsome domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Leapsome domény a adresy URL jednotné přihlašování informace](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://www.leapsome.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Leapsome domény a adresy URL jednotné přihlašování informace](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Adresa URL odpovědi a přihlašování předchozí hodnota adresy URL není skutečné hodnoty. Aktualizujte tyto a skutečné hodnoty, což je vysvětleno později v tomto kurzu.

5. Aplikace Leapsome očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad.
    
    ![Konfigurovat jednotné přihlašování](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Názvový prostor |
    | ---------------| --------------- | --------- |   
    | FirstName | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Příjmení | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | název | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Obrázek | Adresa URL obrázku zaměstnance | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Hodnota atributu obrázek není skutečné. Aktualizujte tuto hodnotu s adresou URL skutečný obrázek. Chcete-li získat obraťte se na tuto hodnotu [tým podpory Leapsome klienta](mailto:support@leapsome.com).
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. V **Namespace** textovému poli, zadejte identifikátor uri oboru názvů na příslušném řádku.
    
    e. Klikněte na tlačítko **Ok**

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/leapsome-tutorial/tutorial_general_400.png)
    
9. Na **Leapsome konfigurace** klikněte na tlačítko **konfigurace Leapsome** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

10. V okně prohlížeče jiný web Přihlaste se k Leapsome jako správce zabezpečení.

11. Vpravo nahoře klikněte na nastavení logo a pak klikněte na tlačítko **nastavení správce**. 

    ![Sada Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

12. V levé nabídce klikněte na tlačítko **jednotné přihlašování na (SSO)** a na **na základě SAML jednotné přihlašování (SSO)** stránky proveďte následující kroky:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Vyberte **na základě SAML povolit jednotné přihlašování**.

    b. Kopírování **adresu URL pro přihlášení (přejděte sem uživatelé spustit přihlášení)** a vložte ji do **přihlašovací adresa URL** textového pole v **Leapsome domény a adresy URL** části na portálu Azure.

    c. Kopírování **adresa URL odpovědi (recieves odpověď od zprostředkovatele identity)** a vložte ji do **adresa URL odpovědi** textového pole v **Leapsome domény a adresy URL** části na portálu Azure.

    d. V **adresu URL pro přihlášení SSO (získaný od zprostředkovatele identity)** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. Zkopírujte certifikát si stáhli z portálu Azure bez – certifikát BEGIN a END certifikát – komentáře a vložte jej do **certifikátu (od zprostředkovatele identity)** textové pole.

    f. Klikněte na tlačítko **nastavení jednotného přihlašování k aktualizaci**.
    
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/leapsome-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/leapsome-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/leapsome-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-leapsome-test-user"></a>Vytvoření zkušebního uživatele Leapsome

V této části vytvoříte volal Britta Simon v Leapsome uživatele. Práce s [tým podpory Leapsome klienta](mailto:support@leapsome.com) přidat uživatele nebo doménu, která musí být seznam povolených adres Leapsome platformy. Pokud doména je přidána tým, se získat uživatelé automaticky zřizovat Leapsome platformě. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Leapsome.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Leapsome, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Leapsome**.

    ![V seznamu aplikací na Leapsome odkaz](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Leapsome na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Leapsome.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

