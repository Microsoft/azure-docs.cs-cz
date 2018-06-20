---
title: 'Kurz: Azure Active Directory integrace s Riskware | Microsoft Docs'
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
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2062ba20b09e8e42fd094fbff628f5e76b5ea00f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225785"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Kurz: Azure Active Directory integrace s Riskware

V tomto kurzu zjistěte, jak integrovat Riskware s Azure Active Directory (Azure AD).

Integrace Riskware s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Riskware.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Riskware (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Riskware, potřebujete následující položky:

- Předplatné služby Azure AD
- Riskware jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Riskware z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-riskware-from-the-gallery"></a>Přidání Riskware z Galerie
Při konfiguraci integrace Riskware do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Riskware z galerie.

**Pokud chcete přidat Riskware z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Riskware**, vyberte **Riskware** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Riskware v seznamu výsledků](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Riskware podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Riskware je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Riskware musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Riskware, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Riskware](#create-a-riskware-test-user)**  – Pokud chcete mít protějšek Britta Simon v Riskware propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Riskware.

**Ke konfiguraci Azure AD jednotné přihlašování s Riskware, proveďte následující kroky:**

1. Na portálu Azure na **Riskware** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. Na **Riskware domény a adresy URL** část, proveďte následující kroky:

    ![Riskware domény a adresy URL jednotné přihlašování informace](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. V **přihlásit na adrese URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKČNÍMU| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` | 
    |||

    b. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL:
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PRODUKČNÍMU| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` | 
    |||

    > [!NOTE] 
    > Přihlašování hodnota adresy URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) k získání hodnoty.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/riskware-tutorial/tutorial_general_400.png)

6. Na **Riskware konfigurace** klikněte na tlačítko **konfigurace Riskware** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Riskware jako správce.

8. Vpravo nahoře klikněte na tlačítko **údržby** chcete otevřít stránku údržby. 

    ![Udržovat Riskware konfigurace](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. Na stránce údržby klikněte na tlačítko **ověřování**.

    ![Konfigurace Riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. V **konfiguraci ověřování** proveďte následující kroky:

    ![Konfigurace Riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Vyberte **typ** jako **SAML** pro ověřování.

    b. V **kód** textovému poli, zadejte kód jako AZURE_UAT.

    c. V **popis** textovému poli, zadejte do pole, jako je konfigurace AZURE pro jednotné přihlašování.

    d. V **jediné přihlášení na stránce** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. V **Odhlásit se stránka** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    f. V **pole formuláře Post** textovému poli, zadejte název pole, která je součástí Post odpovědi, která obsahuje SAML jako SamlResponse

    g. V **název značky XML Identity** textovému poli, typ atributu, který obsahuje jedinečný identifikátor v odpovědi SAML jako NameID.

    h. Otevřete stažené **soubor Xml s metadaty** z portálu Azure v programu Poznámkový blok, zkopírujte certifikát ze souboru metadat a vložte ji do **certifikát** textbox
    
    i. V **příjemce URL** textovému poli, vložte hodnotu **adresa URL odpovědi**, který můžete získat z tým podpory.

    j. V **vystavitele** textovému poli, vložte hodnotu **identifikátor**, který můžete získat z tým podpory.

    > [!Note]
    > Obraťte se na [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) získat tyto hodnoty

    k. Vyberte **použití POST** k předávání žádostí SAML jako parametr post.

    l. Vyberte **žádosti SAML použití** předat jednotné přihlašování povolit žádosti SAML pro SP inicioval.

    m. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/riskware-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/riskware-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/riskware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-riskware-test-user"></a>Vytvoření zkušebního uživatele Riskware

Pokud chcete povolit uživatelům Azure AD přihlášení k Riskware, musí být zřízená do Riskware. V Riskware zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k Riskware jako správce zabezpečení.

2. Vpravo nahoře klikněte na tlačítko **údržby** chcete otevřít stránku údržby. 

    ![Konfigurace Riskware udržuje](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. Na stránce údržby klikněte na tlačítko **osoby**.

    ![Konfigurace Riskware osoby](./media/riskware-tutorial/tutorial_riskware_people.png)

4. Na **podrobnosti** kartu, proveďte následující kroky:
    
    ![Podrobnosti o konfiguraci Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Vyberte **typ osoby** jako jsou třeba zaměstnanců.

    b. V **křestní jméno** textovému poli, zadejte jméno uživatele jako **Britta**.

    c. V **Přezdívka** textovému poli, zadejte příjmení uživatele jako **Simon**.

5. Na **zabezpečení** kartu, proveďte následující kroky:    

    ![Riskware konfigurace zabezpečení](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. V části **ověřování** vyberte **ověřování** režim, ve kterém můžete nastavit jako konfigurace AZURE pro jednotné přihlašování.

    b. V části **přihlášení podrobnosti** v části **ID uživatele** textovému poli, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    c. V **heslo** textovému poli, zadejte heslo uživatele.

6. Na **organizace** kartu, proveďte následující kroky:

    ![Konfigurace Riskware org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. V části **organizace** vyberte organizace jako **Level1** organizace.
    
    b. V části **primární síti na pracovišti osoby** v části **umístění** textovému poli, zadejte vaše umístění.

    c. V části **zaměstnanec** vyberte **stavu zaměstnance** jako volný čas.

7. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Riskware.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Riskware, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Riskware**.

    ![V seznamu aplikací na Riskware odkaz](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Riskware na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Riskware.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
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

