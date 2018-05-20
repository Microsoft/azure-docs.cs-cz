---
title: 'Kurz: Azure Active Directory integrace s Slack | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: c3fb9eb3e8dd4b8c3add5dcf0436958500aa0189
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Kurz: Azure Active Directory integrace s Slack

V tomto kurzu zjistěte, jak integrovat Slack s Azure Active Directory (Azure AD).

Integrace systému Slack s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k systému Slack
- Můžete povolit uživatelům, aby automaticky získat přihlášení k systému Slack (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Slack, potřebujete následující položky:

- Předplatné služby Azure AD
- Slack jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Slack z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-slack-from-the-gallery"></a>Přidání Slack z Galerie
Pokud chcete nakonfigurovat integraci systému Slack do služby Azure AD, potřebujete přidat Slack z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Slack z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Slack**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. Na panelu výsledků vyberte **Slack**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Slack podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Slack je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Slack musí navázat.

V systému Slack, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Slack, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření Slack testovacího uživatele](#creating-a-slack-test-user)**  – Pokud chcete mít protějšek Britta Simon v Slack propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Slack.

**Ke konfiguraci Azure AD jednotné přihlašování s Slack, proveďte následující kroky:**

1. Na portálu Azure na **Slack** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. Na **Slack domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.slack.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://slack.com`

    > [!NOTE] 
    > Hodnota není skutečné. Budete muset aktualizovat hodnotu s skutečné přihlašovací na adresy URL. Obraťte se na [tým podpory Slack](https://slack.com/help/contact) k získání hodnoty.
     
4. Slack aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Pokud máte uživatele, kteří je přiřazen **e-mailová adresa** není na licenci, Office 365, **User.Email** deklarace identity se nezobrazí v tokenu SAML. V těchto případech doporučujeme pomocí **user.userprincipalname** jako **User.Email** atribut hodnota k namapování jako **jedinečný identifikátor** místo.

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogovém okně, vyberte **user.mail** jako **uživatelský identifikátor** a pro každý řádek v tabulce níže, proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    | User.Email | User.Mail |  
    | User.Username | user.userprincipalname |

    a. Klikněte na **atribut** otevřete **Upravit atribut** dialogové okno pole a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    b. Z **hodnotu** vyberte hodnotu atributu zobrazený pro tento řádek.

    c. Ponechte **Namespace** prázdné.
    
    d. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. Na **Slack konfigurace** klikněte na tlačítko **konfigurace Slack** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  V okně prohlížeče jiný web Přihlaste se k webu Slack společnosti jako správce.

10.  Přejděte na **Microsoft Azure AD** pak přejděte na **nastavení Team**.

     ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  V **nastavení Team** klikněte na položku **ověřování** a pak klikněte **změnit nastavení**.

     ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. Na **nastavení ověřování SAML** dialogové okno, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  V **SAML 2.0 koncový bod (HTTP)** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    b.  V **vystavitele zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    c.  Otevřete soubor stažený certifikátu v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte jej do **veřejný certifikát** textové pole.

    d. Konfiguruje nastavení výše uvedených tří vhodnou pro váš tým Slack. Další informace o nastavení najít **příručce Konfigurace jednotného přihlašování k systému Slack na** sem. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klikněte na tlačítko **uložte konfiguraci**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-slack-test-user"></a>Vytváření Slack zkušebního uživatele

Cílem této části je vytvoření uživatele volal Britta Simon v Slack. Slack podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k systému Slack, pokud ještě neexistuje.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k systému Slack.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k systému Slack, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Slack**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Slack na přístupovém panelu jste měli získat automaticky přihlášení k aplikaci Slack.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

