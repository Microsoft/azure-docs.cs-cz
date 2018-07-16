---
title: 'Kurz: Integrace Azure Active Directory s Autotask pracoviště | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Autotask pracoviště.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8e83e6dc66ec6192db589a0964e235c395a26eba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048102"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Kurz: Integrace Azure Active Directory s Autotask pracoviště

V tomto kurzu se dozvíte, jak integrovat Autotask pracoviště s Azure Active Directory (Azure AD).

Integrace Autotask pracoviště s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti Autotask
- Uživatele, aby automaticky získat přihlášení k síti na pracovišti Autotask (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Autotask síti na pracovišti, potřebujete následující položky:

- S předplatným služby Azure AD
- Síti na pracovišti Autotask jednotného přihlašování povolená předplatné
- Musíte být správce nebo správce super v síti na pracovišti.
- Musíte mít účet správce ve službě Azure AD.
- Uživatele, kteří budou tuto funkci využít, musí mít účty v síti na pracovišti a Azure AD a jejich e-mailové adresy pro obě se musí shodovat.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Autotask síti na pracovišti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-autotask-workplace-from-the-gallery"></a>Přidání Autotask síti na pracovišti z Galerie
Pokud chcete nakonfigurovat integraci pracovní plocha Autotask do služby Azure AD, budete muset přidat Autotask síti na pracovišti z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Autotask síti na pracovišti z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Autotask pracoviště**vyberte **Autotask pracoviště** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Autotask síti na pracovišti v seznamu výsledků](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Autotask pracoviště podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v síti na pracovišti Autotask je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v síti na pracovišti Autotask musí být vytvořeno.

V síti na pracovišti Autotask, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Autotask síti na pracovišti, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele síti na pracovišti Autotask](#create-an-autotask-workplace-test-user)**  – Pokud chcete mít protějšek Britta Simon Autotask pracovišti, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Autotask pracoviště.

**Ke konfiguraci Azure AD jednotné přihlašování s Autotask pracoviště, postupujte následovně:**

1. Na webu Azure Portal na **Autotask pracoviště** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. Pokud chcete nakonfigurovat aplikace v **zprostředkovatele identity** iniciované režimu, následující postup proveďte **Autotask pracoviště domény a adresy URL** části:

    ![Autotask pracoviště domény a adresy URL jednotné přihlašování – informace pro zprostředkovatele identity](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

4. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu, kontrola **zobrazit pokročilé nastavení URL** a proveďte následující kroky:

    ![Autotask pracoviště domény a adresy URL jednotné přihlašování – informace pro SP](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Autotask pracoviště klienta](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) k získání těchto hodnot. 

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/autotaskworkplace-tutorial/tutorial_general_400.png)

7. V okně jiné webové prohlížeče Přihlaste se k síti na pracovišti Online pomocí přihlašovacích údajů správce.

    >[!Note]
    >Při konfiguraci zprostředkovatele identity, bude potřeba zadat subdomény. Potvrďte správné subdomény, přihlaste se k síti na pracovišti Online. Po přihlášení, poznamenejte si poddomény v adrese URL.
    >Poddomény je ta část mezi "https://" a ".awp.autotask.net/" a musí být v USA, Evropa, certifikační autority nebo Austrálie.

8. Přejděte na **konfigurace** > **Single Sign-On** a proveďte následující kroky:

    ![Autotask Single Sign-on konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Vyberte **soubor metadat XML** možnost a potom ho nahrajete **soubor XML s metadaty** stáhnout z webu Azure portal.

    b. Klikněte na tlačítko **povolení jednotného přihlašování**.
    
    ![Autotask Single Sign-on schválit konfigurace](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Vyberte **potvrzuji, že tyto informace jsou správné a Věřím tohoto zprostředkovatele identity** zaškrtávací políčko.

    d. Klikněte na tlačítko **schválit**.
     
>[!Note]
>Pokud potřebujete pomoc s konfigurací Autotask síti na pracovišti, najdete [na této stránce](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a vyžádejte si pomoc s vaším účtem v síti na pracovišti.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/autotaskworkplace-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/autotaskworkplace-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/autotaskworkplace-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/autotaskworkplace-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-autotask-workplace-test-user"></a>Vytvořit testovacího uživatele síti na pracovišti Autotask

V této části vytvoříte uživatele v Autotask jako Britta Simon. Prosím práci s [tým podpory Autotask pracoviště](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) k přidání uživatelů v síti na pracovišti Autotask platformy.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k síti na pracovišti Autotask.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k síti na pracovišti Autotask Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Autotask pracoviště**.

    ![Odkaz Autotask síti na pracovišti v seznamu aplikací](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Autotask pracoviště na přístupovém panelu, vám by měl získat automaticky přihlášení k síti na pracovišti Autotask aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/autotaskworkplace-tutorial/tutorial_general_203.png

