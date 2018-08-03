---
title: 'Kurz: Integrace Azure Active Directory s použitím OpsGenie | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a použitím OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 715035072ddc2ceb087d003dd5da5bc47572e9b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444347"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Kurz: Integrace Azure Active Directory s použitím OpsGenie

V tomto kurzu se dozvíte, jak integrovat použitím OpsGenie s Azure Active Directory (Azure AD).

Použitím OpsGenie integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k použitím OpsGenie
- Můžete povolit uživatelům, aby automaticky získat přihlášení k použitím OpsGenie (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím OpsGenie, potřebujete následující položky:

- S předplatným služby Azure AD
- Použitím OpsGenie jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání použitím OpsGenie z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-opsgenie-from-the-gallery"></a>Přidání použitím OpsGenie z Galerie
Konfigurace integrace použitím OpsGenie do služby Azure AD, budete muset přidat použitím OpsGenie z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat použitím OpsGenie z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **použitím OpsGenie**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. Na panelu výsledků vyberte **použitím OpsGenie**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím OpsGenie podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v použitím OpsGenie je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v použitím OpsGenie musí být vytvořeno.

V použitím OpsGenie, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím OpsGenie, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele použitím OpsGenie](#creating-a-opsgenie-test-user)**  – Pokud chcete mít protějšek Britta Simon použitím OpsGenie, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci použitím OpsGenie.

**Ke konfiguraci Azure AD jednotné přihlašování s použitím OpsGenie, proveďte následující kroky:**

1. Na webu Azure Portal na **použitím OpsGenie** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Na **použitím OpsGenie domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.opsgenie.com/auth/login`

1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Na **použitím OpsGenie konfigurace** klikněte na tlačítko **nakonfigurovat použitím OpsGenie** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurace jednotného přihlašování](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Otevřete jiná instance prohlížeče a potom přihlásit k použitím OpsGenie jako správce.

1. Klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Single Sign On** kartu.
   
    ![Přihlašování jedním použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Pokud chcete povolit jednotné přihlašování, vyberte **povoleno**.
   
    ![Nastavení použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. V **poskytovatele** klikněte na tlačítko **Azure Active Directory** kartu.
   
    ![Nastavení použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Na stránce dialogové okno Azure Active Directory proveďte následující kroky:
   
    ![Nastavení použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. V **koncový bod SAML 2.0** vložit do textového pole **jednotné přihlašování na adresu URL služby**hodnotu, která jste zkopírovali z portálu Azure portal.
    
    b. V **adresa Url metadat:** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z portálu Azure portal.
    
    c. Klikněte na tlačítko **uložit změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-opsgenie-test-user"></a>Vytvoření zkušebního uživatele použitím OpsGenie

Cílem této části je vytvořte uživatele Britta Simon v použitím OpsGenie. 

1. V okně webového prohlížeče Přihlaste se jako správce tenanta použitím OpsGenie.

1. Přejděte do seznamu uživatelů kliknutím **uživatele** v levém panelu.
   
   ![Nastavení použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Klikněte na tlačítko **přidat uživatele**.

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
   ![Nastavení použitím OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. V **e-mailu** textového pole zadejte e-mailovou adresu BrittaSimon zákazníky a vyřešené v Azure Active Directory.
   
   b. V **jméno a příjmení** textové pole, typ **Britta Simon**.
   
   c. Klikněte na **Uložit**. 

>[!NOTE]
>Britta dostane e-mail s pokyny k nastavení svého profilu.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu použitím OpsGenie.

![Přiřadit uživatele][200] 

**Chcete použitím OpsGenie přiřadit Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **použitím OpsGenie**.

    ![Konfigurace jednotného přihlašování](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici použitím OpsGenie na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci použitím OpsGenie.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png

