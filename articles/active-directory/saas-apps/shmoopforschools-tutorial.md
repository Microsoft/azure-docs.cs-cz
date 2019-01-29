---
title: 'Kurz: Integrace Azure Active Directory s Shmoop pro školy | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Shmoop pro školy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 90d4cb3c5da37e8de63a95e65670cfd9e3098453
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178422"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Kurz: Integrace Azure Active Directory s Shmoop pro školy

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Shmoop pro školy.

Integrace Shmoop pro školy s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Shmoop pro školy.
- Můžete povolit uživatelům, aby automaticky získat přihlášení pro Shmoop pro školy pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Shmoop pro školy, potřebujete následující položky:

- Předplatné Azure AD
- Shmoop pro školy jednotného přihlašování povolená předplatného

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, doporučujeme:

- Pomocí vašeho produkčního prostředí evironment pouze v případě, že je nutné.
- Začínáme [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/) Pokud ještě nemáte prostředí zkušební verzi Azure AD.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Shmoop pro školy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Přidání Shmoop pro školy z Galerie
Konfigurace integrace Shmoop pro školy do služby Azure AD, budete muset přidat Shmoop pro školy v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Shmoop pro školy v galerii, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko nahoře dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Shmoop pro školy**. Vyberte **Shmoop pro školy** ve výsledcích vyberte **přidat** tlačítko pro přidání aplikace.

    ![Shmoop pro školy v seznamu výsledků](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Shmoop pro školy podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo tento uživatel protějšky v Shmoop pro školy je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatelské v Shmoop pro školy.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Shmoop pro školy, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele Shmoop pro školy](#create-a-shmoop-for-schools-test-user) mít protějšek Britta Simon Shmoop pro školy, který je propojený s Azure AD zastoupení uživatele.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Shmoop pro školy.

**Ke konfiguraci Azure AD jednotné přihlašování s Shmoop pro školy, proveďte následující kroky:**

1. Na webu Azure Portal na **Shmoop pro školy** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. V **jednotného přihlašování** dialogové okno, v rozevírací nabídce v části **režim jednotného přihlašování**vyberte **přihlašování na základě SAML**.
 
    ![Jednotné přihlašování – dialogové okno](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. V **Shmoop pro školy domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. V **identifikátor** pole, zadejte adresu URL s následujícím vzorem: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Obraťte se [tým podpory Shmoop pro školy klienta](mailto:support@shmoop.com) k získání těchto hodnot. 
 
4. Aplikace Shmoop pro školy očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje postup při konfiguraci kontrolní výrazy:

    ![Konfigurace jednotného přihlašování](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop školy podporuje dvě role pro uživatele: **Učitelů** a **Student**. Nastavení těchto rolí ve službě Azure AD tak, aby uživatelům je možné přiřadit příslušné role. Chcete-li pochopit, jak nakonfigurovat role ve službě Azure AD, přečtěte si téma [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).
    
5. V **atributy uživatele** tématu **jednotného přihlašování** dialogového okna nastavte atribut tokenu SAML, jak je znázorněno na předchozím obrázku.  Pak proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. Chcete-li otevřít **přidat atribut** dialogu **přidat atribut**.
    
    ![Konfigurace jednotného přihlašování ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. V **název** zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** vyberte hodnotu atributu, který je zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Vyberte **OK**.

6. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Ke konfiguraci jednotného přihlašování na **Shmoop pro školy** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory Shmoop pro školy](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvořte testovacího uživatele Britta Simon na webu Azure Portal.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Vytvoření zkušebního uživatele Shmoop pro školy

Cílem této části je vytvořte uživatele Britta Simon v Shmoop pro školy. Shmoop pro školy podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud nový uživatel ještě neexistuje, vytvoří se při pokusu o přístup k Shmoop pro školy.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Shmoop pro školy](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Shmoop pro školy.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Shmoop pro školy, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Pak přejděte na **podnikové aplikace** v zobrazení adresáře.  V dalším kroku vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Shmoop pro školy**.

    ![Odkaz Shmoop pro školy v seznamu aplikací](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů.

6. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko. 

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete **Shmoop pro školy** na přístupovém panelu, dlaždice, by měl získat automaticky přihlásíte do aplikace Shmoop pro školy.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

