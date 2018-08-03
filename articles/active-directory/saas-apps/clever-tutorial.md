---
title: 'Kurz: Integrace Azure Active Directory se službou Clever | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 483d03fcc72e0a93111d10b0221164459de27d12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431857"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Kurz: Integrace Azure Active Directory se službou Clever

V tomto kurzu se dozvíte, jak integrovat Clever s Azure Active Directory (Azure AD).

Clever integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Clever.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Clever (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Clever, potřebujete následující položky:

- S předplatným služby Azure AD
- Dokonalá jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Clever z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-clever-from-the-gallery"></a>Přidání Clever z Galerie
Konfigurace integrace Clever do služby Azure AD, budete muset přidat Clever z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Clever z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Clever**vyberte **Clever** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Chytřejší v seznamu výsledků](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Clever podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Clever je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Clever potřeba navázat.

V Clever, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clever, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořte dokonalá testovacího uživatele](#create-a-clever-test-user)**  – Pokud chcete mít protějšek Britta Simon Clever, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci chytřejší.

**Ke konfiguraci Azure AD jednotné přihlašování s Clever, proveďte následující kroky:**

1. Na webu Azure Portal na **Clever** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. Na **chytřejší domény a adresy URL** části, proveďte následující kroky:

    ![Chytřejší domény a adresy URL jednotného přihlašování – informace](./media/clever-tutorial/tutorial_clever_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://clever.com/in/<companyname>`

    b. V **identifikátor** textového pole zadejte adresu URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Přihlašování byste použili hodnotu URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta na dokonalá](https://clever.com/about/contact/) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.
    
    ![Konfigurace jednotného přihlašování](./media/clever-tutorial/tutorial_metadataurl.png)

1. Chytřejší aplikace očekává, že kontrolní výrazy SAML v určitém formátu, které je potřeba přidat vlastní atribut mapování vaší **atributy tokenu SAML** konfigurace.

    Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/clever-tutorial/tutorial_clever_07.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |
    | clever.Teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | jméno  | user.givenname |
    | Příjmení  | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** textové pole prázdné.
    
    d. Klikněte na tlačítko **OK**.
    
1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/clever-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti na dokonalá jako správce.

1. Na panelu nástrojů klikněte na tlačítko **rychlé přihlášení**.

    ![Rychlé přihlášení](./media/clever-tutorial/ic798984.png "rychlé přihlášení")

    > [!NOTE]
    > Abyste ji mohli otestovat jednotné přihlašování, budete muset kontaktovat [tým podpory klienta na dokonalá](https://clever.com/about/contact/) pro povolení jednotného přihlašování k Office 365 v back-endu.

1. Na **rychlé přihlášení** stránce, proveďte následující kroky:
    
      ![Rychlé přihlášení](./media/clever-tutorial/ic798985.png "rychlé přihlášení")
    
      a. Typ **přihlašovací adresa URL**.
    
      >[!NOTE]
      >**Přihlašovací adresa URL** je vlastní hodnotu. Kontakt [tým podpory klienta na dokonalá](https://clever.com/about/contact/) tuto výhodu získáte.
    
      b. Jako **systém identit**vyberte **služby AD FS**.

      c. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z portálu Azure portal.
    
      d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/clever-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/clever-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/clever-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/clever-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-clever-test-user"></a>Vytvořte dokonalá testovacího uživatele

Přihlaste se k Clever Azure AD uživatelům umožnit, musí být poskytnuty do Clever.

V případě Clever, pracovat s [tým podpory klienta na dokonalá](https://clever.com/about/contact/) přidat uživatele na dokonalá platformě. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

>[!NOTE]
>Můžete použít všechny ostatní uživatele na dokonalá účet nástroje pro tvorbu nebo rozhraní API poskytovaných Clever zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Clever použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit Clever Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Clever**.

    ![Odkaz Clever v seznamu aplikací](./media/clever-tutorial/tutorial_clever_app.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dokonalá dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k chytřejší aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

