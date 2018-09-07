---
title: 'Kurz: Integrace Azure Active Directory se službou Slack | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b742f3eb9124093bcf0c3c912bbae0367cdcce56
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053835"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Kurz: Integrace Azure Active Directory se službou Slack

V tomto kurzu se dozvíte, jak integrovat Slack s Azure Active Directory (Azure AD).

Integrace Slack s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup na Slack
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Slack (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Slack, potřebujete následující položky:

- Předplatné Azure AD
- Slack jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Slack z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-slack-from-the-gallery"></a>Přidání Slack z Galerie
Konfigurace integrace Slack do služby Azure AD, budete muset přidat Slack z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Slack z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Slack**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/tutorial_slack_search.png)

1. Na panelu výsledků vyberte **Slack**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Slack podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Slack je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Slack.

V Slack, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Slack, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se uživatel Slack testovací](#creating-a-slack-test-user)**  – Pokud chcete mít protějšek Britta Simon Slack, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Slack.

**Ke konfiguraci Azure AD jednotné přihlašování s Slack, proveďte následující kroky:**

1. Na webu Azure Portal na **Slack** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Na **Slack domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.slack.com`

    b. V **identifikátor** textové pole, zaktualizujte příslušnou hodnotu přihlašovací adresa URL. Toto je vaše doména pracovního prostoru. Příklad: `https://contoso.slack.com`

1. Slack aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Pokud máte uživatele, kteří se přiřadí **e-mailová adresa** není na licenci Office 365 **User.Email** deklarace identity se nezobrazí v tokenu SAML. V těchto případech doporučujeme používat **user.userprincipalname** jako **User.Email** atribut hodnota k namapování jako **jedinečný identifikátor** místo.

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, vyberte **user.mail** jako **identifikátor uživatele** a pro každý řádek je znázorněno v v tabulce níže, proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    | User.Email | User.Mail |  
    | User.Username | user.userprincipalname |

    a. Klikněte na **atribut** otevřete **Upravit atribut** dialogové okno pole a postupujte podle následujících kroků:

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    b. Z **hodnotu** vyberte hodnotu atributu zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_general_400.png)

1. Na **Slack konfigurace** klikněte na tlačítko **nakonfigurovat Slack** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu Slack společnosti jako správce.

1. Přejděte do **Microsoft Azure AD** pak přejděte na **nastavení týmu**.

     ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_001.png)

1. V **nastavení týmu** klikněte na tlačítko **ověřování** kartu a potom klikněte na tlačítko **změnit nastavení**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_002.png)

1. Na **nastavení ověřování SAML** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/slack-tutorial/tutorial_slack_003.png)

    a.  V **SAML 2.0 koncový bod (HTTP)** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    b.  V **Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    c.  V poznámkovém bloku otevřete soubor stažený certifikát, zkopírujte obsah ho do schránky a vložte jej do **veřejný certifikát** textového pole.

    d. Výše uvedené tři nastavení nakonfigurujte podle potřeby pro váš tým Slack. Další informace o nastavení, najdete **Příručka pro konfiguraci jednotného přihlašování pro Slack** tady. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klikněte na tlačítko **uložit konfiguraci**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/create_aaduser_03.png)

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/slack-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-slack-test-user"></a>Vytváření Slack testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Slack. Slack podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Slack, pokud ještě neexistuje. Slack také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](slack-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na Slack.

![Přiřadit uživatele][200]

**Pokud chcete přiřadit Britta Simon na Slack, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Slack**.

    ![Konfigurace jednotného přihlašování](./media/slack-tutorial/tutorial_slack_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na Slack dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Slack.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
