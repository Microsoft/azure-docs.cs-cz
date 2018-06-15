---
title: 'Kurz: Azure Active Directory integrace s Clever | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: d151aba43a6795c5a691077fe6729fc9853012bf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341480"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Kurz: Azure Active Directory integrace s Clever

V tomto kurzu zjistěte, jak integrovat Clever s Azure Active Directory (Azure AD).

Integrace Clever s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Clever.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Clever (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Clever, potřebujete následující položky:

- Předplatné služby Azure AD
- Inteligentní jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Clever z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-clever-from-the-gallery"></a>Přidání Clever z Galerie
Při konfiguraci integrace Clever do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Clever z galerie.

**Pokud chcete přidat Clever z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Clever**, vyberte **Clever** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Inteligentní v seznamu výsledků](./media/active-directory-saas-clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clever podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Clever je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Clever musí navázat.

V Clever, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clever, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření inteligentní zkušebního uživatele](#create-a-clever-test-user)**  – Pokud chcete mít protějšek Britta Simon v Clever propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci inteligentní.

**Ke konfiguraci Azure AD jednotné přihlašování s Clever, proveďte následující kroky:**

1. Na portálu Azure na **Clever** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-clever-tutorial/tutorial_clever_samlbase.png)

3. Na **inteligentní domény a adresy URL** část, proveďte následující kroky:

    ![Inteligentní domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-clever-tutorial/tutorial_clever_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://clever.com/in/<companyname>`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Přihlášení hodnota adresy URL není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory inteligentní klienta](https://clever.com/about/contact/) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-clever-tutorial/tutorial_metadataurl.png)

5. Inteligentní aplikace očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu SAML** konfigurace.

    Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-clever-tutorial/tutorial_clever_07.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |
    | clever.Teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | FirstName  | user.givenname |
    | Příjmení  | user.surname |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-clever-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-clever-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** textové pole prázdné.
    
    d. Klikněte na tlačítko **OK**.
    
7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-clever-tutorial/tutorial_general_400.png)

8. V okně prohlížeče jiný web Přihlaste se k webu inteligentní společnosti jako správce.

9. Na panelu nástrojů klikněte na tlačítko **rychlé přihlášení**.

    ![Rychlé přihlášení](./media/active-directory-saas-clever-tutorial/ic798984.png "rychlé přihlášení")

    > [!NOTE]
    > Chcete-li otestovat jednotné přihlašování, budete muset kontaktovat [tým podpory inteligentní klienta](https://clever.com/about/contact/) pro povolení jednotného přihlašování Office 365 v back-end.

10. Na **rychlé přihlášení** proveďte následující kroky:
    
      ![Rychlé přihlášení](./media/active-directory-saas-clever-tutorial/ic798985.png "rychlé přihlášení")
    
      a. Typ **přihlašovací adresa URL**.
    
      >[!NOTE]
      >**Přihlašovací adresa URL** je vlastní hodnota. Obraťte se na [tým podpory inteligentní klienta](https://clever.com/about/contact/) získat tuto hodnotu.
    
      b. Jako **systém identit**, vyberte **služby AD FS**.

      c. V **adresu URL metadat** textovému poli, vložte **adresu Url aplikace federační Metadata** hodnotu, která jste zkopírovali z portálu Azure.
    
      d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-clever-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-clever-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-clever-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-clever-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-clever-test-user"></a>Vytvoření inteligentní zkušebního uživatele

Pokud chcete povolit uživatelům Azure AD přihlášení k Clever, musí být zřízená do Clever.

V případě Clever, pracovat s [tým podpory inteligentní klienta](https://clever.com/about/contact/) přidat uživatele do inteligentní platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

>[!NOTE]
>Můžete použít jakékoli jiné nástroje vytvoření inteligentní uživatelského účtu nebo rozhraní API poskytované Clever ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Clever.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Clever, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Clever**.

    ![Odkaz Clever v seznamu aplikací](./media/active-directory-saas-clever-tutorial/tutorial_clever_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici inteligentní na přístupovém panelu jste měli získat automaticky přihlášení k aplikaci inteligentní.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clever-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clever-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clever-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clever-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clever-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clever-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clever-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clever-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clever-tutorial/tutorial_general_203.png

