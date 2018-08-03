---
title: 'Kurz: Integrace Azure Active Directory pomocí tajného klíče serveru (v místním prostředí) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tajný kód serveru (místní).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 30a1498ab41f263c77656400c4200313048cc331
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436161"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Kurz: Integrace Azure Active Directory pomocí tajného klíče serveru (místní)

V tomto kurzu se dozvíte, jak integrovat tajný klíč serveru (v místním prostředí) se službou Azure Active Directory (Azure AD).

Integrace serveru tajný kód (v místním prostředí) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k serveru tajný kód (v místním prostředí).
- Můžete povolit uživatelům, aby automaticky získat přihlášení k serveru tajný kód (v místním prostředí) (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s tajný klíč serveru (místní), potřebujete následující položky:

- S předplatným služby Azure AD
- Tajný kód serveru (místní) jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tajného klíče serveru (místní) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Přidání tajného klíče serveru (místní) z Galerie
Konfigurace integrace tajný klíč serveru (místní) do služby Azure AD, budete muset přidat tajný klíč serveru (místní) z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání tajného klíče serveru (místní) z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **tajný klíč serveru (místní)** vyberte **tajný klíč serveru (místní)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Server tajných klíčů (v místním prostředí) v seznamu výsledků](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí tajného klíče serveru (v místním prostředí) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek tajný klíč serveru (v místním prostředí) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel tajný klíč serveru (místní).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí tajného klíče serveru (místní), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele tajný klíč serveru (místní)](#create-a-secret-server-on-premises-test-user)**  – Pokud chcete mít protějšek Britta Simon tajný klíč serveru (místní), který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci tajný klíč serveru (místní).

**Ke konfiguraci Azure AD jednotné přihlašování pomocí tajného klíče serveru (místní), proveďte následující kroky:**

1. Na webu Azure Portal na **tajný klíč serveru (místní)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Na **tajný klíč serveru (místní) domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Tajného kódu serveru (místní) domény a adresy URL jednotného přihlašování – informace](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. V **identifikátor** textového pole zadejte uživatele zvolena hodnota jako například: `https://secretserveronpremises.azure`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > ID Entity je znázorněno výše je jenom jako příklad a můžete libovolně vybrat jedinečnou hodnotu, který identifikuje vaši instanci tajný klíč serveru ve službě Azure AD. Je nutné odeslat toto ID Entity [tým podpory tajný klíč serveru (místní) klienta](https://thycotic.force.com/support/s/) a jejich konfigurace na své straně. Další informace, přečtěte si prosím [v tomto článku](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Tajného kódu serveru (místní) domény a adresy URL jednotného přihlašování – informace](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory tajný klíč serveru (místní) klienta](https://thycotic.force.com/support/s/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Zkontrolujte **zobrazit pokročilé nastavení podepisování certifikátu** a vyberte **podepisování možnost** jako **přihlašování SAML odpověď a kontrolní výraz**.

    ![Možnosti podpisu](./media/secretserver-on-premises-tutorial/signing.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. Na **konfigurace tajný klíč serveru (místní)** klikněte na tlačítko **konfigurace tajný klíč serveru (místní)** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace tajného kódu serveru (místní)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Ke konfiguraci jednotného přihlašování na **tajný klíč serveru (místní)** straně, je nutné odeslat na stažený **Certificate(Base64), adresa URL odhlašování, SAML jednotné přihlašování – adresa URL služby**, a **SAML Entity ID** k [tým podpory tajný klíč serveru (místní)](https://thycotic.force.com/support/s/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Vytvoření zkušebního uživatele tajný klíč serveru (místní)

V této části vytvořte uživatele Britta Simon tajný klíč serveru (místní). Práce s [tým podpory tajný klíč serveru (místní)](https://thycotic.force.com/support/s/) přidat uživatele na platformě tajný klíč serveru (místní). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k serveru tajný kód (v místním prostředí).

![Přiřazení role uživatele][200]

**Britta Simon přiřadit k serveru tajný kód (v místním prostředí), proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **tajný klíč serveru (místní)**.

    ![Tajný kód serveru (místní) odkaz v seznamu aplikací](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici tajný klíč serveru (v místním prostředí) na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci tajný klíč serveru (místní).
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

