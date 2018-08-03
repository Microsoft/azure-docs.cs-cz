---
title: 'Kurz: Integrace Azure Active Directory s e-mailem SkyDesk | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SkyDesk e-mailu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 058aad72ea8e5741bc632b3c27c032613683ae78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444078"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Kurz: Integrace Azure Active Directory s SkyDesk e-mailem

V tomto kurzu se dozvíte, jak integrovat SkyDesk e-mailu s Azure Active Directory (Azure AD).

Integrace SkyDesk e-mailu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k e-mailu SkyDesk
- Uživatele, aby automaticky získat přihlášeného k e-mailu SkyDesk (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SkyDesk e-mailu, potřebujete následující položky:

- S předplatným služby Azure AD
- E-mailu SkyDesk jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební zde [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání e-mailu SkyDesk z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-skydesk-email-from-the-gallery"></a>Přidání e-mailu SkyDesk z Galerie
Konfigurace integrace SkyDesk e-mailu do služby Azure AD, budete muset přidat SkyDesk e-mailu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SkyDesk e-mailu z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **e-mailu SkyDesk**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

1. Na panelu výsledků vyberte **e-mailu SkyDesk**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s e-mailem SkyDesk podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek SkyDesk e-mailu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v e-mailu SkyDesk potřeba navázat.

V e-mailu SkyDesk, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SkyDesk e-mailu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele e-mailu SkyDesk](#creating-a-skydesk-email-test-user)**  – Pokud chcete mít protějšek Britta Simon v e-mailu SkyDesk, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SkyDesk e-mailu.

**Ke konfiguraci Azure AD jednotné přihlašování s SkyDesk e-mailem, proveďte následující kroky:**

1. Na webu Azure Portal na **e-mailu SkyDesk** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

1. Na **SkyDesk e-mailová doména a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory SkyDesk e-mailovém klientovi](https://www.skydesk.sg/support/) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_general_400.png)

1. Na **SkyDesk e-mailové konfigurace** klikněte na tlačítko **konfigurace e-mailu SkyDesk** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

1. Chcete povolit jednotné přihlašování v **e-mailu SkyDesk**, proveďte následující kroky:

    a. Přihlášení ke svému účtu e-mailu SkyDesk jako správce.

    b. V nabídce v horní části klikněte na tlačítko **nastavení**a vyberte **organizace**. 
    
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Klikněte na **domén** na levém panelu.
    
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Klikněte na **přidání domény**.
    
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Zadejte název domény a pak ověřte doménu.
    
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Klikněte na **ověřování SAML** na levém panelu.
    
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na **ověřování SAML** dialogového okna stránky, proveďte následující kroky:
   
      ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Použití ověřování na základě SAML, měli byste buď mít **ověřené domény** nebo **portál URL** instalační program. Na portálu můžete nastavit adresu URL s jedinečným názvem.
    > 
    > 
   
    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
   
    b. V **odhlášení** textového pole URL vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    c. **Změňte heslo URL** je volitelný takže políčko nechte prázdné.

    d. Klikněte na **získáte klíč ze souboru** vyberte svůj certifikát stažený z webu Azure portal a potom klikněte na tlačítko **otevřít** na kterou certifikát nahrajete.

    e. Jako **algoritmus**vyberte **RSA**.

    f. Klikněte na tlačítko **Ok** a uložte změny.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Vytvoření zkušebního uživatele SkyDesk e-mailu

V této části vytvořte uživatele Britta Simon SkyDesk e-mailem.

1. Klikněte na **přístup uživatelů** z levé strany panelu SkyDesk e-mailem a pak zadejte svoje uživatelské jméno. 

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Pokud je potřeba vytvořit uživatele hromadné, budete muset požádat [tým podpory SkyDesk e-mailovém klientovi](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k e-mailu SkyDesk.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit k e-mailu SkyDesk Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **e-mailu SkyDesk**.

    ![Konfigurace jednotného přihlašování](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SkyDesk e-mailu na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci SkyDesk e-mailu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

