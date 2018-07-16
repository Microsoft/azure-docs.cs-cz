---
title: 'Kurz: Integrace Azure Active Directory s vyrovnat se s LMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a chránit před LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 066ae92056e4b80b6627b371d6ebeb3235b2781d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043774"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Integrace Azure Active Directory s vyrovnat se s LMS

V tomto kurzu se dozvíte, jak integrovat LMS vyrovnat se s Azure Active Directory (Azure AD).

Chránit před LMS integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vyrovnat se s LMS.
- Můžete povolit uživatelům přihlásit se automaticky chránit před LMS (přes jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Pokud chcete získat další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vyrovnat se s LMS, potřebujete následující položky:

- S předplatným služby Azure AD
- Se vyrovnávají s LMS jednotného přihlašování povolená předplatného

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro účely tohoto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání vyrovnat se s LMS z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-absorb-lms-from-the-gallery"></a>Přidání vyrovnat se s LMS z Galerie
Pokud chcete nakonfigurovat integraci vyrovnat se s LMS do služby Azure AD, přidejte vyrovnat se s LMS z Galerie na váš seznam spravovaných aplikací SaaS.

Chcete-li přidat vyrovnat se s LMS z galerie, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** tlačítko.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vyrovnat se s LMS**vyberte **vyrovnat se s LMS** v způsobit panelu a pak vyberte **přidat** tlačítko.

    ![Chránit před LMS v seznamu výsledků](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí vyrovnat se s LMS podle volá Britta Simon testovacího uživatele.

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek vyrovnat se s LMS je ve službě Azure AD. Jinými slovy je potřeba vytvořit vztah mezi uživatelem ve službě Azure AD a odpovídajícího uživatele v LMS vyrovnat se s.

Navázání tohoto vztahu odkazu přiřazením *uživatelské jméno* hodnota ve službě Azure AD, jako *uživatelské jméno* hodnotu v LMS vyrovnat se s.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vyrovnat se s LMS, dokončete stavební bloky v příštích pěti oddílů.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování aplikace se vyrovnávají s LMS.

Chcete-li nakonfigurovat LMS vyrovnat se s Azure AD jednotného přihlašování, postupujte takto:

1. Na webu Azure Portal na **vyrovnat se s LMS** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. V **jednotného přihlašování** v dialogu **režimu** vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. V **vyrovnat se s LMS domény a adresy URL** části, postupujte takto:

    ![Chránit před LMS domény a adresy URL jednotné přihlašování – informace](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. V **identifikátor** zadejte adresu URL, která používá následující syntaxi: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. V **adresy URL odpovědi** zadejte adresu URL, která používá následující syntaxi: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Tyto adresy URL nejsou skutečné hodnoty. Aktualizujte je skutečná identifikátor a adresy URL odpovědí. Chcete-li tyto hodnoty získat, obraťte se [tým podpory klienta vyrovnat se s LMS](https://www.absorblms.com/support). 

4. V **podpisový certifikát SAML** sekci **Stáhnout** sloupci vyberte **soubor XML s metadaty**a poté uložte soubor metadat do počítače.

    ![Odkaz ke stažení podpisového certifikátu](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Vyberte **Uložit**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. V **vyrovnat se s konfigurací LMS** vyberte **konfigurace LMS vyrovnat se s** otevřete **nakonfigurovat přihlašování** okna a pak nakopírujte **odhlašování URL** v **Stručná referenční příručka oddílu.**

    ![V podokně Konfigurace LMS vyrovnat se s](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. V novém okně webového prohlížeče Přihlaste se k serveru vaší společnosti LMS vyrovnat se s jako správce.

8. Vyberte **účet** tlačítko v pravé horní části. 

    ![Tlačítko účet](./media/absorblms-tutorial/1.png)

9. V podokně účtu vyberte **nastavení portálu**.

    ![Odkaz Nastavení portálu](./media/absorblms-tutorial/2.png)
    
10. Vyberte kartu **Uživatelé**.

    ![Kartu uživatelé](./media/absorblms-tutorial/3.png)

11. Na stránce Konfigurace jednotného přihlašování postupujte takto:

    ![Na stránce Konfigurace jednotného přihlašování](./media/absorblms-tutorial/4.png)

    a. V **režimu** vyberte **iniciované zprostředkovatele Identity**.

    b. V poznámkovém bloku otevřete certifikát, který jste si stáhli z webu Azure portal. Odeberte **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** značky. Potom v **klíč** vložte zbývající obsah.
    
    c. V **Vlastnost Id** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele ve službě Azure AD. Například pokud *userPrincipalName* je vybrána ve službě Azure AD, vyberte **uživatelské jméno**.

    d. V **přihlašovací adresa URL** pole, vložte **adresa URL portálu User Access** z vaší aplikace **vlastnosti** stránky na webu Azure portal.

    e. V **odhlašovací adresa URL**, vložte **odhlašování URL** hodnotu, kterou jste zkopírovali z **nakonfigurovat přihlašování** okna na webu Azure portal.

12. Přepnout **pouze povolení přihlášení SSO** k **na**.

    ![Přepnout pouze povolení přihlášení SSO](./media/absorblms-tutorial/5.png)

13. Vyberte **uložit.**

> [!TIP]
> Stručné verzi těchto pokynů si můžete přečíst [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání aplikace **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a přístup k vložený dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal.

![Vytvořit testovacího uživatele Azure AD][100]

Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte takto:

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/absorblms-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/absorblms-tutorial/create_aaduser_02.png) 

3. V horní části dialogového okna, vyberte **přidat**.
 
    ![Tlačítko Přidat](./media/absorblms-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, postupujte takto:
 
    ![Dialogové okno uživatele](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** textového pole zadejte e-mailovou adresu Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu v **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="create-an-absorb-lms-test-user"></a>Vytvořit testovacího uživatele vyrovnat se s LMS

Přihlaste se k LMS vyrovnat se s Azure AD uživatelům musí být nastavené v LMS vyrovnat se s.  

Instalační program pro vyrovnat se s LMS úlohu.

Chcete-li nastavit uživatelský účet, postupujte takto:

1. Přihlaste se k webu společnosti LMS vyrovnat se s jako správce.

2. V levém podokně vyberte **uživatelé**.

    ![Odkaz Vyrovnat se s LMS uživatele](./media/absorblms-tutorial/absorblms_users.png)

3. V **uživatelé** vyberte **uživatelé**.

    ![Odkaz uživatele](./media/absorblms-tutorial/absorblms_userssub.png)

4. V **přidat nový** rozevíracího seznamu vyberte **uživatele**.

    ![Přidat nový rozevírací seznam](./media/absorblms-tutorial/absorblms_createuser.png)

5. Na **přidat uživatele** stránce, postupujte takto:

    ![Stránka Přidat uživatel](./media/absorblms-tutorial/user.png)

    a. V **křestní jméno** zadejte jméno, například **Britta**.

    b. V **příjmení** zadejte příjmení, jako například **Simon**.
    
    c. V **uživatelské jméno** zadejte úplný název, jako například **Britta Simon**.

    d. V **heslo** zadejte heslo Britta Simon.

    e. V **Potvrdit heslo** pole, zadejte heslo znovu.
    
    f. Nastavte **je aktivní** přepnutím **aktivní**.  

6. Vyberte **uložit.**
 
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k vyrovnat se s LMS.

![Přiřazení role uživatele][200]

Chcete vyrovnat se s LMS přiřadit uživatele Britta Simon, postupujte takto:

1. Na webu Azure Portal, otevřete zobrazení aplikací, přejděte do zobrazení adresáře a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Odkaz "Všechny aplikace"][201] 

2. V **aplikací** seznamu vyberte **vyrovnat se s LMS**.

    ![Chránit před LMS odkaz v seznamu aplikací](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

4. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** v dialogu **uživatelé** seznamu vyberte **Britta Simon**.

6. V **uživatelů a skupin** dialogové okno, vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Na přístupovém panelu vyberte **vyrovnat se s LMS** dlaždice se automaticky přihlásí vám do vaší aplikace se vyrovnávají s LMS. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png
