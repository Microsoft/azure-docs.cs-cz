---
title: 'Kurz: Integrace Azure Active Directory se službou Riskware | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.openlocfilehash: 7705baa0ba912f24d7859110c75d36703aeb4a77
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041955"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Kurz: Integrace Azure Active Directory se službou Riskware

V tomto kurzu se dozvíte, jak integrovat Riskware s Azure Active Directory (Azure AD).

Riskware integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Riskware.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Riskware (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Riskware, potřebujete následující položky:

- S předplatným služby Azure AD
- Riskware jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Riskware z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-riskware-from-the-gallery"></a>Přidání Riskware z Galerie
Konfigurace integrace Riskware do služby Azure AD, budete muset přidat Riskware z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Riskware z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Riskware**vyberte **Riskware** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Riskware v seznamu výsledků](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Riskware podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Riskware je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Riskware potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Riskware, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Riskware](#create-a-riskware-test-user)**  – Pokud chcete mít protějšek Britta Simon Riskware, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Riskware.

**Ke konfiguraci Azure AD jednotné přihlašování s Riskware, proveďte následující kroky:**

1. Na webu Azure Portal na **Riskware** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. Na **Riskware domény a adresy URL** části, proveďte následující kroky:

    ![Riskware domény a adresy URL jednotného přihlašování – informace](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKČNÍ| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | UKÁZKA| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL:
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PRODUKČNÍ| `https://riskcloud.net/prod` |
    | UKÁZKA| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) má být získána hodnota.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/riskware-tutorial/tutorial_general_400.png)

6. Na **Riskware konfigurace** klikněte na tlačítko **nakonfigurovat Riskware** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

7. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Riskware jako správce.

8. V pravém horním rohu, klepněte na příkaz **údržby** otevřete stránku údržby.

    ![Udržovat Riskware konfigurace](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. Na stránce Údržba, klikněte na **ověřování**.

    ![Konfigurace Riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. V **konfigurace ověřování** stránce, proveďte následující kroky:

    ![Konfigurace Riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Vyberte **typ** jako **SAML** pro ověřování.

    b. V **kód** textového pole zadejte kód jako AZURE_UAT.

    c. V **popis** textového pole zadejte váš popis, např. konfigurace AZURE pro jednotné přihlašování.

    d. V **jediné přihlášení na stránce** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **odhlásit stránky** vložit do textového pole **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    f. V **pole formuláře Post** textového pole zadejte název pole v odpověď na příspěvek, který obsahuje SAML jako SAMLResponse

    g. V **názvu značky XML Identity** textové pole, typ atributu, který obsahuje jedinečný identifikátor v odpověď SAML NameID.

    h. Otevřete na stažený **soubor Xml s metadaty** z webu Azure portal v programu Poznámkový blok, zkopírujte certifikát ze souboru metadat a vložte ho do **certifikát** textové pole

    i. V **příjemce URL** textového pole vložte hodnotu **adresy URL odpovědi**, které můžete získat na tým podpory.

    j. V **vystavitele** textového pole vložte hodnotu **identifikátor**, které můžete získat na tým podpory.

    > [!Note]
    > Kontakt [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) k získání těchto hodnot

    k. Vyberte **po použití** zaškrtávací políčko.

    l. Vyberte **požadavek SAML, použijte** zaškrtávací políčko.

    m. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/riskware-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/riskware-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/riskware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-riskware-test-user"></a>Vytvoření zkušebního uživatele Riskware

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Riskware, musí být poskytnuty do Riskware. Zřizování v Riskware, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Riskware jako správce zabezpečení.

2. V pravém horním rohu, klepněte na příkaz **údržby** otevřete stránku údržby. 

    ![Udržuje Riskware konfigurace](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. Na stránce Údržba, klikněte na **lidé**.

    ![Konfigurace Riskware osoby](./media/riskware-tutorial/tutorial_riskware_people.png)

4. Vyberte **podrobnosti** kartě a proveďte následující kroky:

    ![Podrobnosti o konfiguraci Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Vyberte **typu Person** zaměstnance, jako je.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

5. Na **zabezpečení** kartu, proveďte následující kroky:

    ![Riskware konfigurace zabezpečení](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. V části **ověřování** vyberte **ověřování** režimu, který jste nastavili, jako je AZURE konfiguraci pro jednotné přihlašování.

    b. V části **přihlašovací údaje** sekci **ID uživatele** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    c. V **heslo** textového pole zadejte heslo uživatele.

6. Na **organizace** kartu, proveďte následující kroky:

    ![Konfigurace Riskware organizace](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Vyberte možnost jako **Level1** organizace.

    b. V části **primární síti na pracovišti uživatele** sekci **umístění** textového pole zadejte vaši polohu.

    c. V části **zaměstnance** vyberte **stavu zaměstnance** neformální, jako je.

7. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Riskware použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Riskware Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Riskware**.

    ![Odkaz Riskware v seznamu aplikací](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Riskware na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Riskware.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

