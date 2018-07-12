---
title: 'Kurz: Integrace Azure Active Directory se službou Adobe Creative Cloud | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2018
ms.author: jeedes
ms.openlocfilehash: e1788de7c2372797b2034eb1753ab435c1299889
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548269"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Kurz: Integrace Azure Active Directory se službou Adobe Creative Cloud

V tomto kurzu se dozvíte, jak integrovat Adobe Creative Cloud s Azure Active Directory (Azure AD).

Integrace Adobe Creative Cloud s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Adobe Creative Cloud.
- Uživatele, aby automaticky získat přihlášení k Adobe Creative Cloud (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Creative Cloud, potřebujete následující položky:

- S předplatným služby Azure AD
- Adobe Creative Cloud jednotného přihlašování povolená předplatného
- Požadovaná verze Adobe Creative Cloud Enterprise

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe Creative Cloud z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative Cloud z Galerie

Pokud chcete nakonfigurovat integraci Adobe Creative Cloud do služby Azure AD, budete muset přidat Adobe Creative Cloud na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Adobe Creative Cloud z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Adobe Creative Cloud**vyberte **Adobe Creative Cloud** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Adobe Creative Cloud v seznamu výsledků](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Adobe Creative Cloud na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Adobe Creative Cloud je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v aplikaci Adobe Creative Cloud je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Creative Cloud, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon v Adobe Creative Cloud, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Adobe Creative Cloud.

**Ke konfiguraci Azure AD jednotné přihlašování s Adobe Creative Cloud, proveďte následující kroky:**

1. Na webu Azure Portal na **Adobe Creative Cloud** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Na **Adobe Creative Cloud domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![Adobe Creative Cloud domény a adresy URL jednotného přihlašování – informace](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.okta.com/saml2/service-provider/<token>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [Adobe Creative Cloud Enterprise](https://www.adobe.com/au/creativecloud/business/teams/plans.html) k získání těchto hodnot.

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Adobe Creative Cloud domény a adresy URL jednotného přihlašování – informace](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte hodnotu jako: `https://adobe.com`

5. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atribut uživatele** kartu aplikace. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | LastName |user.surname |
    | Email |User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Klikněte na tlačítko **OK**.

    > [!NOTE]
    > Uživatelé musí mít platnou licenci Office 365 ExO e-mailu pro hodnoty, který se má naplnit odpověď SAML deklarace identity.

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. Na **Adobe Creative Cloud konfigurace** klikněte na tlačítko **konfigurace Adobe Creative Cloud** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka části**.

    ![Konfigurace služby Adobe Creative Cloud](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. V okně jiné webové prohlížeče, přihlaste se do [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

11. Přejděte na **nastavení** na horním navigačním panelu a klikněte na tlačítko **Identity**. Otevře se seznam domén. Klikněte na tlačítko **konfigurovat** odkaz proti vaší doméně. Pak proveďte následující kroky na **jednotné přihlašování na konfigurace požadované** oddílu. Další informace najdete v tématu [nastavení domény](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Nastavení](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "nastavení")

    a. Klikněte na tlačítko **Procházet** na kterou odešlete certifikát stažený z Azure AD, aby **IDP certifikát**.

    b. V **Vystavitel zprostředkovatele identity** textového pole vložte hodnotu **SAML Entity Id** zkopírovanou z **nakonfigurovat přihlašování** části webu Azure Portal.

    c. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **adresa URL služby jednotného přihlašování SAML** zkopírovanou z **nakonfigurovat přihlašování** části webu Azure Portal.

    d. Vyberte **HTTP - Redirect** jako **IDP vazby**.

    e. Vyberte **e-mailová adresa** jako **nastavení hlavního názvu uživatele přihlášení**.

    f. Klikněte na tlačítko **Uložit** tlačítko.

12. Řídicí panel se teď k dispozici XML **"Stáhnout Metadata"** souboru. Obsahuje EntityDescriptor adresy URL a AssertionConsumerService Adobe. Otevřete soubor a nakonfigurovat v aplikaci Azure AD.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu EntityDescriptor Adobe předpokladu, že pro **identifikátor** na **nakonfigurovat nastavení aplikace** dialogového okna.

    b. Použijte hodnotu AssertionConsumerService Adobe předpokladu, že pro **adresy URL odpovědi** na **nakonfigurovat nastavení aplikace** dialogového okna.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Vytvořit testovacího uživatele Adobe Creative Cloud

Chcete-li povolit uživatele Azure AD k přihlášení do Adobe Creative Cloud, musí být poskytnuty do Adobe Creative Cloud. V případě Adobe Creative Cloud je zřizování úlohu.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ke zřízení uživatelských účtů, proveďte následující kroky:

1. Přihlaste se k [konzoly pro správu Adobe](https://adminconsole.adobe.com) lokality jako správce.

2. Přidat jako federované ID uživatele v rámci konzoly Adobe a přiřadit profil produktu. Podrobné informace o přidávání uživatelů najdete v tématu [přidat uživatele konzoly pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. V tuto chvíli zadejte e-mailovou adresu nebo hlavní název uživatele do formuláře signin Adobe, stisknutím klávesy tab a by měl Federovat zpět do služby Azure AD:
    * Webový přístup: www.adobe.com > přihlášení
    * V rámci aplikace klasické pracovní plochy nástroje > přihlášení
    * V rámci aplikace > Nápověda > přihlášení

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Adobe Creative Cloud.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Adobe Creative Cloud, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Adobe Creative Cloud**.

    ![V seznamu aplikací na odkaz Adobe Creative Cloud](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Adobe Creative Cloud na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Adobe Creative Cloud.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Nastavení domény (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurace Azure pro použití s jednotným Přihlašováním Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/adobe-creative-cloud-tutorial/tutorial_general_203.png
