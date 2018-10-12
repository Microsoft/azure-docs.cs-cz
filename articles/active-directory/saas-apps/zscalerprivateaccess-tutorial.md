---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem privátní přístup (ZPA) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem privátní přístup (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 5858ab1846ccdc2e2b78f2be4173ea3cc17a3605
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116568"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Kurz: Integrace Azure Active Directory se Zscalerem privátní přístup (ZPA)

V tomto kurzu se dozvíte, jak integrovat Zscalerem privátní přístup (ZPA) se službou Azure Active Directory (Azure AD).

Integrace Zscalerem privátní přístup (ZPA) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscalerem privátní přístup (ZPA)
- Uživatele, aby automaticky získat přihlášeného k Zscalerem privátní přístup (ZPA) (jednotné přihlašování) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem privátní přístup (ZPA), potřebujete následující položky:

- Předplatné Azure AD
- Zscalerem privátní přístup (ZPA) jednotného přihlašování povolená předplatného


> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.


Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscalerem privátní přístup (ZPA) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Přidání Zscalerem privátní přístup (ZPA) z Galerie
Konfigurace integrace nástroje Zscalerem privátní přístup (ZPA) do služby Azure AD, budete muset přidat Zscalerem privátní přístup (ZPA) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zscalerem privátní přístup (ZPA) z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Zscalerem privátní přístup (ZPA)**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

1. Na panelu výsledků vyberte **Zscalerem privátní přístup (ZPA)** a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem privátní přístup (ZPA) je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem privátní přístup (ZPA) musí být vytvořeno.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Zscalerem privátní přístup (ZPA).

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Zscalerem privátní přístup (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)**  – Pokud chcete mít v Zscalerem privátní přístup (ZPA), který je propojený s Azure AD reprezentace jí protějšek Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci Zscalerem privátní přístup (ZPA).

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem privátní přístup (ZPA), proveďte následující kroky:**

1. V portálu pro správu Azure na **Zscalerem privátní přístup (ZPA)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
1. Na **Zscalerem privátní přístup (ZPA) domény a adresy URL** části, proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. V **identifikátor** textové pole, typ: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Tady doporučujeme používat jedinečnou hodnotu adresy URL v identifikátoru. Kontakt [tým podpory Zscalerem privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

1. Na **vytvořit nový certifikát** dialogového okna, klikněte na ikonu kalendáře a vyberte **datum vypršení platnosti**. Pak klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

1. Na **podpisový certifikát SAML** vyberte **nastavit nový certifikát jako aktivní** a klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

1. V místní nabídce **certifikát výměny** okna, klikněte na tlačítko **OK**.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Zscalerem privátní přístup (ZPA).

1. Přejděte do **správce** a potom klikněte na tlačítko **konfigurace zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

1. V **konfigurace zprostředkovatele identity** klikněte na tlačítko **přidat nové konfigurace zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

1. V **nové konfigurace zprostředkovatele identity** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Klikněte na tlačítko **vybrat soubor** a nahrát soubor stažený metadat.

    b. Klikněte na tlačítko **Uložit** tlačítko.
    


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Vytvoření zkušebního uživatele Zscalerem privátní přístup (ZPA)

V této části vytvoříte uživateli Britta Simon v Zscalerem privátní přístup (ZPA). Spojte se prosím s [tým podpory Zscalerem privátní přístup (ZPA)](https://help.zscaler.com/zpa-submit-ticket) přidat uživatele na platformě Zscalerem privátní přístup (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Zscalerem privátní přístup (ZPA).

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit k Zscalerem privátní přístup (ZPA) Britta Simon, postupujte následovně:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Zscalerem privátní přístup (ZPA)**.

    ![Konfigurace jednotného přihlašování](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    


### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem privátní přístup (ZPA) na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zscalerem privátní přístup (ZPA).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png