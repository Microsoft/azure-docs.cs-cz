---
title: 'Kurz: Integrace Azure Active Directory s MyWorkDrive | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14cd7bea6707e6f724829a28b57604553e7405c0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199761"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Kurz: Integrace Azure Active Directory s MyWorkDrive

V tomto kurzu se dozvíte, jak integrovat MyWorkDrive s Azure Active Directory (Azure AD).

Integrace s Azure AD MyWorkDrive nabízí následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k MyWorkDrive.
- Můžete povolit uživatelům, aby automaticky získat podepsaný ve službě MyWorkDrive (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

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

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **MyWorkDrive**vyberte **MyWorkDrive** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![MyWorkDrive v seznamu výsledků](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí MyWorkDrive podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v MyWorkDrive je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MyWorkDrive potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s MyWorkDrive, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele MyWorkDrive](#creating-a-myworkdrive-test-user)**  – Pokud chcete mít protějšek Britta Simon MyWorkDrive, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci MyWorkDrive.

**Ke konfiguraci Azure AD jednotné přihlašování s MyWorkDrive, proveďte následující kroky:**

1. Na webu Azure Portal na **MyWorkDrive** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![MyWorkDrive domény a adresy URL jednotného přihlašování – informace](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![MyWorkDrive domény a adresy URL jednotného přihlašování – informace](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL.  Zadejte name:e.g hostitele MyWorkDrive serveru vaší společnosti.
    > 
    > Adresa URL odpovědi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Přihlašovací adresa URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Pokud si nejste jistí, jak nastavit vlastní název hostitele a certifikát SSL pro tyto hodnoty, obraťte se na tým podpory MyWorkDrive klienta.

6. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko kopírování **ikonu** kopírování **adresa Url metadat federace aplikace**a uložte ho do počítače...

    ![Odkaz ke stažení certifikátu](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png)

7. V jiné okno webového prohlížeče, přihlaste se k MyWorkDrive jako správce zabezpečení.

8. Na serveru MyWorkDrive v panelu Správce, klikněte na **ENTERPRISE** a proveďte následující kroky:

    ![Správce](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Povolit **jednotného přihlašování SAML/ADFS**.

    b. Vyberte **SAML - Azure AD**

    c. V **adresa Url federačních metadat aplikace Azure** textového pole vložte hodnotu **adresa Url federačních metadat aplikace** který jste zkopírovali z portálu Azure portal.

    d. Klikněte na **Uložit**.

    >[!NOTE]
    >Pro další informace najdete [článek podpory MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-myworkdrive-test-user"></a>Vytvoření zkušebního uživatele MyWorkDrive

V této části vytvoříte uživatele v MyWorkDrive jako Britta Simon. Práce s [tým podpory MyWorkDrive](mailto:support@myworkdrive.com) přidat uživatele na platformě MyWorkDrive. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k MyWorkDrive použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **MyWorkDrive**.

    ![Konfigurace jednotného přihlašování](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici MyWorkDrive na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci MyWorkDrive.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
