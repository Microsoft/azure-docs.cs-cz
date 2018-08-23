---
title: 'Kurz: Integrace Azure Active Directory se službou živém chatu Comm100 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Comm100 živém chatu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42056583"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Kurz: Integrace Azure Active Directory se službou Comm100 živém chatu

V tomto kurzu se dozvíte, jak integrovat Comm100 živém chatu s Azure Active Directory (Azure AD).

Integrace Comm100 živém chatu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Comm100 živém chatu.
- Uživatele, aby automaticky získat přihlášení k Comm100 živém chatu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Comm100 živém chatu, potřebujete následující položky:

- Předplatné Azure AD
- Živém chatu Comm100 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání živém chatu Comm100 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Přidání živém chatu Comm100 z Galerie
Pokud chcete nakonfigurovat integraci živém chatu Comm100 do služby Azure AD, musíte doplnit Comm100 živém chatu v galerii váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat živém chatu Comm100 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **živém chatu Comm100**vyberte **živém chatu Comm100** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Comm100 živém chatu v seznamu výsledků](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Comm100 živém chatu na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v živém chatu Comm100 je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v živém chatu Comm100 potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Comm100 živém chatu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Comm100 živém chatu](#create-a-comm100-live-chat-test-user)**  – Pokud chcete mít protějšek Britta Simon Comm100 živém chatu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Comm100 živém chatu.

**Ke konfiguraci Azure AD jednotné přihlašování s Comm100 živém chatu, proveďte následující kroky:**

1. Na webu Azure Portal na **živém chatu Comm100** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Na **Comm100 Live domény chatu a adresy URL** části, proveďte následující kroky:

    ![Domény živého chatu Comm100 a adresy URL jednotného přihlašování – informace](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Hodnota přihlašovací adresa URL bude aktualizován skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

4. Živém chatu Comm100 aplikace očekává, že kontrolní výrazy SAML obsahuje konkrétní atributy. Nakonfigurujte následující atributy pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    |  Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |    
    |   e-mail    | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

6. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Na **konfigurace živého chatu Comm100** klikněte na tlačítko **konfigurace živém chatu Comm100** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Comm100 živý Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. V jiné okno webového prohlížeče, přihlaste se k Comm100 živém chatu jako správce zabezpečení.

10. V horní pravé straně stránky klikněte na **Můj účet**.

    ![Myaccount Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. V levé nabídce klikněte na **zabezpečení** a potom klikněte na tlačítko **agenta Single Sign-On**.

    ![Zabezpečení Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Na **agenta Single Sign-On** stránce, proveďte následující kroky:

    ![Zabezpečení Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Zkopírujte první zvýrazněný odkaz a vložte ji **přihlašovací adresa URL** textového pole v **Comm100 Live domény chatu a adresy URL** části na webu Azure portal.

    b. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **zvolte soubor** nahrát base-64 kódování certifikátu, který jste si stáhli z portálu Azure portal do **certifikát**.

    e. Klikněte na tlačítko **uložit změny**

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Vytvoření zkušebního uživatele Comm100 živém chatu

Povolení služby Azure AD uživatelům přihlášení na živém chatu Comm100, musí být poskytnuty do Comm100 živém chatu. Zřizování v živém chatu Comm100, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Comm100 živý Chat jako správce zabezpečení.

2. V horní pravé straně stránky klikněte na **Můj účet**.

    ![Myaccount Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. V levé nabídce klikněte na **agentů** a potom klikněte na tlačítko **nového agenta**.

    ![Agent Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na **nového agenta** stránce, proveďte následující kroky:

    ![Živém chatu Comm100 nového agenta](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    d. V **zobrazovaného názvu** textového pole zadejte zobrazované jméno uživatele, jako je **Britta simon**

    e. V **heslo** textového pole zadejte heslo.

    f. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Comm100 živém chatu.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Comm100 živém chatu, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **živém chatu Comm100**.

    ![Na živém chatu Comm100 odkaz v seznamu aplikací](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Comm100 živém chatu na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Comm100 živém chatu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

