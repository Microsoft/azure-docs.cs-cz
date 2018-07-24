---
title: 'Kurz: Integrace Azure Active Directory se službou základní dovednosti | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a základní dovednosti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.author: jeedes
ms.openlocfilehash: e11ba8ca9c4ad17b2ade909bb474ad2d1fcf4410
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205370"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Kurz: Integrace Azure Active Directory se službou základní dovednosti

V tomto kurzu se dozvíte, jak integrovat základní dovednosti s Azure Active Directory (Azure AD).

Integrace základní dovednosti s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k základní dovednosti.
- Uživatele, aby automaticky získat přihlášení k základní dovednosti (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s základní dovednosti, potřebujete následující položky:

- S předplatným služby Azure AD
- Základní dovednosti jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání základní dovednosti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-skills-base-from-the-gallery"></a>Přidání základní dovednosti z Galerie
Konfigurace integrace základní dovednosti v Azure AD, musíte přidat základní dovednosti v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat základní dovednosti z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **základní dovednosti**vyberte **základní dovednosti** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Základní dovednosti v seznamu výsledků](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí základní dovednosti založené na testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v základní dovednosti je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v základní dovednosti.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s základní dovednosti, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele základní dovednosti](#create-a-skills-base-test-user)**  – Pokud chcete mít protějšek Britta Simon v základní dovednosti, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci základní dovednosti.

**Ke konfiguraci Azure AD jednotné přihlašování s základní dovednosti, proveďte následující kroky:**

1. Na webu Azure Portal na **základní dovednosti** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. Na **domény základní dovednosti a adresy URL** části, proveďte následující kroky:

    ![Domény základní dovednosti a adresy URL jednotného přihlašování – informace](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE] 
    > Z aplikace základní dovednosti můžete získat adresu URL přihlašování. Prosím přihlášení jako správce a přejděte na správu -> Nastavení -> Instance podrobnosti -> místní odkaz. Zkopírujte adresu URL přihlašování a vložte ji vyšší než textovém poli.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/skillsbase-tutorial/tutorial_general_400.png)

6. V jiné okno webového prohlížeče, přihlaste se k základní dovednosti jako správce zabezpečení.

7. V levé nabídce v části **správce** klikněte na tlačítko **ověřování**.

    ![Správce](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. Na **ověřování** stránce vyberte Single Sign-On jako **SAML 2**.

    ![Jedné](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. Na **ověřování** stránce, proveďte následující kroky:

    ![Jedné](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Klikněte na **metadat zprostředkovatele identity aktualizace** vedle **stav** možnost a vložte obsah XML metadat, který jste si stáhli z webu Azure portal do zadaného textového pole.

    > [!Note]
    > Můžete také ověřit metadat zprostředkovatele identity prostřednictvím **metadat validátoru** nástroj jako zvýrazněné ve výše uvedeném snímku obrazovky.

    b. Klikněte na **Uložit**.
    
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/skillsbase-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/skillsbase-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/skillsbase-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-skills-base-test-user"></a>Vytvoření zkušebního uživatele základní dovednosti

Cílem této části je vytvořte uživatele Britta Simon v základní dovednosti. Základní znalosti podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k základní dovednosti, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, postupujte podle pokynů [tady](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k základní dovednosti.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon se základní dovednosti, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **základní dovednosti**.

    ![Odkaz základní dovednosti v seznamu aplikací](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici základní dovednosti na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci základní dovednosti.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

