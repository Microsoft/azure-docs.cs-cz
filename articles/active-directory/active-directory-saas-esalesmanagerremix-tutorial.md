---
title: 'Kurz: Integrovat Azure Active Directory E vedoucí prodeje Remix | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Remix Manager E organizační jednotky prodej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: f681eb91c1e79eb42b572956dfab93e620489e74
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrovat Azure Active Directory Remix vedoucí prodeje E

V tomto kurzu zjistěte, jak integrovat Azure Active Directory (Azure AD) s Remix Manager E prodej.

Díky integraci služby Azure AD se Remix Manager E prodej, získáte následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k E prodej Manager Remix.
- Můžete povolit uživatelům získat automaticky k přihlášený E prodej Manager Remix (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Remix Manager E prodej, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné služby E prodej Manager Remix SSO

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme, abyste provedli *není* použít provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání E prodej Manager Remix z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Přidání E prodej Manager Remix z Galerie
Při konfiguraci integrace služby Azure AD s E prodej Manager Remix přidejte E prodej Manager Remix z Galerie si na seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [portál Azure](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![Okno "Podnikové aplikace"][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** v horní části okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **E prodej Manager Remix**, vyberte **E prodej Manager Remix** v seznamu výsledků a potom vyberte **přidat**.

    ![E prodej Manager Remix v seznamu výsledků](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s E prodej Manager Remix, podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí identifikovat E prodej Manager Remix uživatele a jeho protějšku ve službě Azure AD. Jinými slovy je nutné vytvořit vztah propojení mezi uživatele Azure AD a stejného uživatele v E prodej Manager Remix.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Remix Manager E prodej, dokončete stavebních bloků v následujících pět částech:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci E prodej Manager Remix následujícím způsobem:

1. Na portálu Azure na **E prodej Manager Remix** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Na odkaz "Jednotného přihlašování"][4]

2. V **jednotného přihlašování** okno v **režimu přihlašování** vyberte **na základě SAML přihlašování**.
 
    ![Okno "Jednotného přihlašování"](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. V části **E prodej Manager Remix domény a adresy URL**, postupujte takto:

    ![E prodej Manager Remix domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<serveru na základě URL > /\<subdomény > / esales pc*.

    b. V **identifikátor** pole, zadejte adresu URL v následujícím formátu: *https://\<serveru na základě URL > /\<subdomény > /*.

    c. Poznámka: **identifikátor** hodnoty pro pozdější použití v tomto kurzu.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Aktualizaci s skutečné přihlašovací adresa URL a identifikátor. Chcete-li získat hodnoty, obraťte se na [tým podpory E prodej Manager Remix klienta](mailto:esupport@softbrain.co.jp).

4. V části **SAML podpisový certifikát**, vyberte **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu (Base64)](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Vyberte **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtněte políčko a potom vyberte **emailaddress** atribut.
    
    ![Okno atributy uživatele](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

    **Upravit atribut** otevře se okno.

6. Kopírování **Namespace** a **název** hodnoty. Generovat hodnota ve vzoru  *\<Namespace > /\<Name >* a uložit pro pozdější použití v tomto kurzu.

    ![Okno Upravit atribut](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. V části **E prodej Manager Remix konfigurace**, vyberte **konfigurace Remix Manager E na prodej**.

    ![Konfigurace Remix vedoucí prodeje E](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **Konfigurovat přihlášení** otevře se okno.

8. V **Stručná referenční příručka** část, zkopírujte adresu URL pro odhlášení a adresu URL služby jednotného přihlašování SAML.

9. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

10. Přihlaste se k vaší aplikaci E prodej Manager Remix jako správce.

11. Vyberte v pravé horní **do nabídky správce**.

    ![Příkaz "Na nabídku správce"](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

12. V levém podokně vyberte **nastavení systému** > **spolupráci s externím systému**.

    !["Nastavení systému" a "Spolupráci s externím systému" odkazy](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
13. V **spolupráci s externím systému** vyberte **SAML**.

    ![Okno "Spolupráci s externím systému"](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

14. V části **nastavení ověřování SAML**, postupujte takto:

    ![V části "Nastavení ověřování SAML"](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Vyberte **PC verze** zaškrtávací políčko.
    
    b. V **spolupráce položky** oddílem se v rozevíracím seznamu vyberte **e-mailu**.

    c. V **spolupráce položky** pole, vložte hodnotu deklarace identity, který jste zkopírovali dříve z portálu Azure (to znamená, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. V **vystavitele (entity ID)** pole, vložit hodnotu identifikátor, který jste zkopírovali dříve z **E prodej Manager Remix domény a adresy URL** části portálu Azure.

    e. Chcete-li nahrát svůj certifikát stažený z portálu Azure, vyberte **souboru výběr**.

    f. V **ID zprostředkovatele přihlašovací adresa URL** pole, vložte adresu URL služby jednotného přihlašování SAML, který jste zkopírovali dříve na portálu Azure.

    g. V **adresa URL odhlašovací zprostředkovatele Identity** pole, vložte odhlašování hodnota adresy URL, kterou jste zkopírovali dříve v portálu Azure.

    h. Vyberte **nastavení dokončení**.

> [!TIP]
> Jak při instalaci aplikace, můžete si přečíst stručným verzi podle předchozích pokynů v [portál Azure](https://portal.azure.com). Po přidání aplikace v **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete vložené v dokumentaci **konfigurace** v dolní části. Další informace o funkci embedded dokumentaci najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure, následujícím způsobem:

![Vytvořit testovací uživatele Azure AD][100]

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Odkaz Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam aktuálního uživatele, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** vyberte **přidat**.

    ![Tlačítko Přidat](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Uživatele** otevře se okno.

4. V **uživatele** okno, postupujte takto:

    ![Okno uživatele](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a poznamenejte si hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Vytvoření zkušebního uživatele E prodej Manager Remix

1. Přihlásit se k vaší aplikaci E prodej Manager Remix jako správce.

2. Vyberte **do nabídky správce** v nabídce vpravo nahoře.

    ![Konfigurace Remix vedoucí prodeje E](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Vyberte **nastavení vaší společnosti** > **údržby oddělení a zaměstnanci**a potom vyberte **zaměstnanci zaregistrovat**.

    ![Na kartě "Zaměstnanci zaregistrovat"](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. V **nové registrace zaměstnanec** část, postupujte takto:
    
    !["Registrace nové zaměstnance" části](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. V **jméno zaměstnance** zadejte název uživatele (například **Britta**).

    b. Vyplňte zbývající požadovaná pole.
    
    c. Pokud povolíte SAML, správce nemůže přihlásit z přihlašovací stránky. Udělení správce přihlašovací oprávnění pro uživatele tak, že vyberete **přihlašovací jméno správce** zaškrtávací políčko.

    d. Vyberte **registrace**.

5. V budoucnu se přihlásit jako správce, přihlaste se jako uživatel, který má oprávnění správce a pak vyberte v pravé horní **do nabídky správce**.

    ![Příkaz "Na nabídku správce"](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k E prodej Manager Remix. Chcete-li to provést, postupujte takto: 

![Přiřadit role uživatele][200] 

1. Na portálu Azure otevřete **aplikace** zobrazení, přejděte na **Directory** zobrazit a potom vyberte **podnikové aplikace, které** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

2. V **aplikace** seznamu, vyberte **E prodej Manager Remix**.

    ![Odkaz E prodej Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu, vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici E prodej Manager Remix na přístupovém panelu, můžete by měl být přihlášeni automaticky do vaší aplikace E prodej Manager Remix.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS do Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

