---
title: 'Kurz: Integrace Azure Active Directory se službou FirmPlay - Advocacy zaměstnance pro nábor | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FirmPlay - Advocacy zaměstnance pro nábor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: abc29480e5f5531857cbf973d5386727c3d7f2ac
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114524"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Kurz: Integrace Azure Active Directory se službou FirmPlay - Advocacy zaměstnance pro nábor

V tomto kurzu se dozvíte, jak integrovat FirmPlay - Advocacy zaměstnance pro nábor se službou Azure Active Directory (Azure AD).

Integrace FirmPlay - Advocacy zaměstnance pro nábor s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FirmPlay - Advocacy zaměstnance pro nábor
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FirmPlay - Advocacy zaměstnance pro nábor (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FirmPlay - Advocacy zaměstnance pro nábor, potřebujete následující položky:

- Předplatné Azure AD
- FirmPlay - Advocacy zaměstnance pro přijetí jednotného přihlašování povolená předplatného


> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.


Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FirmPlay - Advocacy zaměstnance pro nábor z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Přidání FirmPlay - Advocacy zaměstnance pro nábor z Galerie
Ke konfiguraci integrace FirmPlay - Advocacy zaměstnance pro nábor do služby Azure AD, budete muset přidat FirmPlay - Advocacy zaměstnance pro nábor z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat FirmPlay - Advocacy zaměstnance pro nábor z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **FirmPlay - Advocacy zaměstnance pro nábor**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/tutorial_firmplay_001.png)

1. Na panelu výsledků vyberte **FirmPlay - Advocacy zaměstnance pro nábor**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s FirmPlay - Advocacy zaměstnance pro nábor podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatel v FirmPlay – Advocacy zaměstnance pro nábor je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v FirmPlay - Advocacy zaměstnance pro nábor potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v FirmPlay - Advocacy zaměstnance pro nábor.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FirmPlay - Advocacy zaměstnance pro nábor, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření FirmPlay - Advocacy zaměstnance pro testovacího uživatele nábor](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  – Pokud chcete mít protějšek Britta Simon FirmPlay: Advocacy zaměstnance pro přijetí, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování ve vašich FirmPlay - zaměstnance Advocacy nábor aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s FirmPlay - Advocacy zaměstnance pro nábor, proveďte následující kroky:**

1. V portálu pro správu Azure na **FirmPlay - Advocacy zaměstnance pro nábor** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_01.png)

1. Na **FirmPlay - Advocacy zaměstnanci, přijetí domény a adresy URL** sekci **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Všimněte si, že to není skutečné hodnoty. Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [FirmPlay - Advocacy zaměstnance pro tým podpory nábor](mailto:engineering@firmplay.com) tuto výhodu získáte. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

1. Na **vytvořit nový certifikát** dialogového okna, klikněte na ikonu kalendáře a vyberte **datum vypršení platnosti**. Pak klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_general_300.png)

1. Na **podpisový certifikát SAML** vyberte **nastavit nový certifikát jako aktivní** a klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_04.png)

1. V místní nabídce **certifikát výměny** okna, klikněte na tlačítko **OK**.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_general_400.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (base64)** a uložte soubor certifikátu v počítači. 

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

1. Na **FirmPlay - Advocacy zaměstnance pro přijetí konfigurace** klikněte na tlačítko **konfigurace FirmPlay - Advocacy zaměstnance pro nábor** otevřete **nakonfigurovat přihlašování** Dialogové okno.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_07.png)

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, kontaktujte [FirmPlay - Advocacy zaměstnance pro tým podpory nábor](mailto:engineering@firmplay.com) a poskytnout následující: 

    • Na stažený **soubor certifikátu**

    • **Adresu URL služby jednotného přihlašování SAML**

    • **SAML Entity ID**

    • **Adresa URL pro odhlášení**
  

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Vytváření FirmPlay - Advocacy zaměstnance pro nábor testovacího uživatele

V této části vytvoříte uživatele v FirmPlay - Advocacy zaměstnance pro nábor jako Britta Simon. Spojte se prosím s [FirmPlay - Advocacy zaměstnance pro tým podpory nábor](mailto:engineering@firmplay.com) k přidání uživatelů do FirmPlay - Advocacy zaměstnance pro platformu nábor.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k FirmPlay - Advocacy zaměstnance pro nábor.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon FirmPlay - Advocacy zaměstnance pro nábor, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **FirmPlay - Advocacy zaměstnance pro nábor**.

    ![Konfigurace jednotného přihlašování](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    


### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na FirmPlay - Advocacy zaměstnance pro nábor dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší FirmPlay - zaměstnance Advocacy nábor aplikace.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png