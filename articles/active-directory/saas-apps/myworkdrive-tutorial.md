---
title: 'Kurz: Integrace Azure Active Directory se službou MyWorkDrive | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094052"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Kurz: Integrace Azure Active Directory se službou MyWorkDrive

V tomto kurzu se dozvíte, jak integrovat MyWorkDrive s Azure Active Directory (Azure AD).

Integrace s Azure AD MyWorkDrive nabízí následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k MyWorkDrive.
- Můžete povolit uživatelům, aby automaticky získat podepsaný ve službě MyWorkDrive (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s MyWorkDrive, potřebujete následující položky:

- Předplatné Azure AD
- MyWorkDrive jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání MyWorkDrive z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-myworkdrive-from-the-gallery"></a>Přidání MyWorkDrive z Galerie
Konfigurace integrace MyWorkDrive do služby Azure AD, budete muset přidat MyWorkDrive v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat MyWorkDrive z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/myworkdrive-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/myworkdrive-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **MyWorkDrive**vyberte **MyWorkDrive** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí MyWorkDrive podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v MyWorkDrive je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MyWorkDrive potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s MyWorkDrive, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele MyWorkDrive](#create-a-myworkdrive-test-user)**  – Pokud chcete mít protějšek Britta Simon MyWorkDrive, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci MyWorkDrive.

**Ke konfiguraci Azure AD jednotné přihlašování s MyWorkDrive, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **MyWorkDrive** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Klikněte na **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL.  Zadejte name:e.g hostitele MyWorkDrive serveru vaší společnosti.
    > 
    > Adresa URL odpovědi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Přihlašovací adresa URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Pokud si nejste jistí, jak nastavit vlastní název hostitele a certifikát SSL pro tyto hodnoty, obraťte se na tým podpory MyWorkDrive klienta.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko kopírování **ikonu** kopírování **adresa Url metadat federace aplikace** a klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** uložit ve vašem počítači.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. Na **nastavení MyWorkDrive** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    Všimněte si, že adresa URL může třeba následující:

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Pokud chcete nakonfigurovat jednotné přihlašování na straně MyWorkDrive, stáhněte si **certifikát (Base64), URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** a ruční konfigurace na serveru MyWorkDrive nebo zkopírujte a vložte Azure  **Adresa URL federačních metadat aplikace** do Azure SAML Panel správce serveru MyWorkDrive obrazovku pro konfiguraci AD. Další informace získáte [tým podpory MyWorkDrive](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/myworkdrive-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/myworkdrive-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-myworkdrive-test-user"></a>Vytvoření zkušebního uživatele MyWorkDrive

V této části vytvoříte uživatele v MyWorkDrive jako Britta Simon. Práce s [tým podpory MyWorkDrive](mailto:support@myworkdrive.com) přidat uživatele na platformě MyWorkDrive. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k MyWorkDrive použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/myworkdrive-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **MyWorkDrive**.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/myworkdrive-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici MyWorkDrive na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci MyWorkDrive.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
