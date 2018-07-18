---
title: 'Kurz: Integrace Azure Active Directory se službou Cloud od společnosti Atlassian | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cloud od společnosti Atlassian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: jeedes
ms.openlocfilehash: 68613b8613a2e5a9139b83eb23e66884659efc47
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114930"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Kurz: Integrace Azure Active Directory se službou Cloud od společnosti Atlassian

V tomto kurzu se dozvíte, jak integrovat Cloud od společnosti Atlassian s Azure Active Directory (Azure AD).

Integrace od společnosti Atlassian cloudu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup pro Cloud od společnosti Atlassian.
- Můžete povolit uživatelům, aby automaticky přihlášení (jednotné přihlašování) pro Cloud od společnosti Atlassian pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s cloudem od společnosti Atlassian, potřebujete následující položky:

- Předplatné služby Azure AD.
- Chcete-li povolit zabezpečení kontrolního výrazu SAML (Markup Language) jednotného přihlašování pro Cloud od společnosti Atlassian produkty, nastavení přístupu od společnosti Atlassian. Další informace o [přístupu od společnosti Atlassian]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme je velmi riskantní používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání Cloud od společnosti Atlassian z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-atlassian-cloud-from-the-gallery"></a>Přidání Cloud od společnosti Atlassian z Galerie
Konfigurace integrace produktů od společnosti Atlassian cloudu s Azure AD, přidejte Cloud od společnosti Atlassian z Galerie váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    !["Nové aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **Cloud od společnosti Atlassian**, v seznamu výsledků vyberte **Cloud od společnosti Atlassian**a pak vyberte **přidat**.

    ![Cloud od společnosti Atlassian v seznamu výsledků](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, nakonfigurovat a otestovat Azure AD jednotné přihlašování s cloudem od společnosti Atlassian, na základě testovací uživatele s názvem *Britta Simon*.

Pro jednotné přihlašování pro práci služba Azure AD potřebuje k identifikaci uživatelů od společnosti Atlassian cloudu a jeho protějšek ve službě Azure AD. Jinými slovy je potřeba vytvořit vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v cloudu od společnosti Atlassian.

K navázání vztahu odkazu, přiřaďte jako Cloud od společnosti Atlassian *uživatelské jméno* stejnou hodnotu, která je přiřazena k Azure AD *uživatelské jméno*.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s cloudem od společnosti Atlassian, budete muset provést stavební bloky v následujících částech.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Cloud od společnosti Atlassian.

Ke konfiguraci Azure AD jednotné přihlašování s cloudem od společnosti Atlassian, postupujte takto:

1. Na webu Azure Portal v **Cloud od společnosti Atlassian** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. V **jednotného přihlašování** okno v **režim jednotného přihlašování** vyberte **přihlašování na základě SAML**.

    ![Okno jednotné přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Konfigurace aplikace v **zahájené pomocí IDP** režim, v části **od společnosti Atlassian Cloudová doména a adresy URL**, postupujte takto:

    ![Cloud od společnosti Atlassian domény a adresy URL jednotného přihlašování – informace](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. V **identifikátor** pole, zadejte adresu URL s následujícím vzorem: `https://auth.atlassian.com/saml/<unique ID>`.
    
    b. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`.

    c. Zkontrolujte **zobrazit pokročilé nastavení URL**.

    d. V **stav přenosu** pole, zadejte adresu URL s následujícím vzorem: `https://<instancename>.atlassian.net`.

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Zobrazí se tyto skutečné hodnoty z konfigurace SAML služby Cloud od společnosti Atlassian obrazovky, který je vysvětlen později v tomto kurzu.

4. Konfigurace aplikace v režimu iniciovaného Zprostředkovatelem přihlašování, vyberte **zobrazit pokročilé nastavení URL** a pak na **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://<instancename>.atlassian.net`.

    ![Cloud od společnosti Atlassian domény a adresy URL jednotného přihlašování – informace](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Předchozí přihlašovací adresa URL není skutečný. Skutečná adresa URL přihlašování zaktualizujte příslušnou hodnotu. Kontakt [tým podpory od společnosti Atlassian cloudu klienta](https://support.atlassian.com/) tuto výhodu získáte.

5. V části **podpisový certifikát SAML**vyberte **Certificate(Base64)** a poté uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Od společnosti Atlassian cloudové aplikace očekává kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. 

    Ve výchozím nastavení **identifikátor uživatele** hodnota je namapována na user.userprincipalname. Tato hodnota k namapování k user.mail změňte. Můžete také zvolit jakoukoli jinou odpovídající hodnotu podle nastavení vaší organizace, ale ve většině případů by mělo fungovat e-mailu.

    ![Odkaz ke stažení certifikátu](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Vyberte **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Chcete-li otevřít **nakonfigurovat přihlašování** okno v **konfigurace služby Cloud od společnosti Atlassian** vyberte **nakonfigurovat Cloud od společnosti Atlassian**.

9. V **Stručná referenční příručka** tématu, zkopírujte **SAML Entity ID** a **SAML jednotné přihlašování – adresa URL služby**.

    ![Konfigurace služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Chcete-li získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přihlaste se k portálu od společnosti Atlassian pomocí přihlašovacích údajů správce.

11. Budete muset ověřit doménu před přechodem ke konfiguraci jednotného přihlašování. Další informace najdete v tématu [ověření domény od společnosti Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentu.

12. V levém podokně vyberte **SAML jednotného přihlašování**. Pokud jste tak již neučinili, přihlásit k odběru pro správce identit od společnosti Atlassian.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. V **konfigurace přidat SAML** okno, postupujte takto:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. V **zprostředkovatele Identity Entity ID** vložte ID entity SAML, který jste zkopírovali z portálu Azure portal.

    b. V **zprostředkovatele Identity adresu URL jednotného přihlašování** pole, vložte adresu URL služby jednotného přihlašování SAML, který jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z portálu Azure portal v souboru .txt, zkopírujte hodnotu (bez *začít certifikát* a *End Certificate* řádků) a vložte jej do **veřejné X509 certifikát** pole.
    
    d. Vyberte **uložit konfiguraci**.
     
14. Aby bylo zajištěno, že jste nastavili správné adresy URL, aktualizujte nastavení Azure AD následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. V okně SAML Kopírovat **SP Identity ID** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **domény a adresy URL**, vložte ji **identifikátor** pole.
    
    b. V okně SAML Kopírovat **SP adresa URL služby příjemce kontrolního výrazu** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **domény a adresy URL**, vložte ji **adresy URL odpovědi** pole. Přihlašovací adresa URL je adresa URL tenanta Cloud od společnosti Atlassian.

    > [!NOTE]
    > Pokud jste stávající zákazník, po aktualizaci **SP Identity ID** a **SP adresa URL služby příjemce kontrolního výrazu** hodnoty na webu Azure Portal, vyberte **Ano, aktualizujte konfiguraci**. Pokud jste nového zákazníka, můžete tento krok přeskočit.
    
15. Na webu Azure Portal, vyberte **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

   ![Vytvořit testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. V **všichni uživatelé** okně **přidat**.

    ![Tlačítko Přidat](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. V **uživatele** okno, postupujte takto:

    ![V okně uživatele](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="create-an-atlassian-cloud-test-user"></a>Vytvořit testovacího uživatele Cloud od společnosti Atlassian

Pokud chcete povolit Azure AD uživatelům umožní přihlásit se na Cloud od společnosti Atlassian, zřízení uživatelských účtů v cloudu od společnosti Atlassian ručně následujícím způsobem:

1. V **správu** vyberte **uživatelé**.

    ![Odkaz uživatelé cloudových služeb od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Chcete-li vytvořit uživatele v cloudu od společnosti Atlassian, vyberte **pozvání uživatele**.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. V **e-mailová adresa** zadejte e-mailovou adresu uživatele a pak přiřadit přístup k aplikaci.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Chcete-li odeslat e-mailová pozvánka pro uživatele, vyberte **pozvat uživatele**. Uživateli se odešle e-mailovou pozvánku a po přijetí e-mailové pozvánce, je uživatel aktivní v systému.

>[!NOTE]
>Můžete také hromadně – vytvořit uživatele tak, že vyberete **Hromadně vytvořit** tlačítko **uživatelé** části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure díky udělení přístupu pro Cloud od společnosti Atlassian. Chcete-li to provést, postupujte takto:

![Přiřazení role uživatele][200]

1. Na webu Azure Portal, otevřete **aplikací** zobrazit, přejděte do zobrazení adresáře a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V **aplikací** seznamu vyberte **Cloud od společnosti Atlassian**.

    ![V seznamu aplikací na odkaz Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu vyberte **Britta Simon**.

6. V **uživatelů a skupin** okně **vyberte**.

7. V **přidat přiřazení** okně **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete **Cloud od společnosti Atlassian** dlaždici na přístupovém panelu, můžete musejí být podepsána automaticky do vaší aplikace Cloud od společnosti Atlassian.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
