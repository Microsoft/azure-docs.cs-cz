---
title: 'Kurz: Integrace Azure Active Directory se službou YouEarnedIt | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a YouEarnedIt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: jeedes
ms.openlocfilehash: 3a394c13092547991bf7f8ae98e5c69e92077701
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344780"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Kurz: Integrace Azure Active Directory se službou YouEarnedIt

V tomto kurzu se dozvíte, jak integrovat YouEarnedIt s Azure Active Directory (Azure AD).

YouEarnedIt integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k YouEarnedIt.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k YouEarnedIt (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s YouEarnedIt, potřebujete následující položky:

- Předplatné Azure AD
- YouEarnedIt jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání YouEarnedIt z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-youearnedit-from-the-gallery"></a>Přidání YouEarnedIt z Galerie

Konfigurace integrace YouEarnedIt do služby Azure AD, budete muset přidat YouEarnedIt z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat YouEarnedIt z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **YouEarnedt**vyberte **YouEarnedt** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![YouEarnedIt v seznamu výsledků](./media/youearnedit-tutorial/tutorial_youearnedit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí YouEarnedIt podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v YouEarnedIt je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v YouEarnedIt potřeba navázat.

V YouEarnedIt, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s YouEarnedIt, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele YouEarnedIt](#create-a-youearnedit-test-user)**  – Pokud chcete mít protějšek Britta Simon YouEarnedIt, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci YouEarnedIt.

**Ke konfiguraci Azure AD jednotné přihlašování s YouEarnedIt, proveďte následující kroky:**

1. Na webu Azure Portal na **YouEarnedIt** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/youearnedit-tutorial/tutorial_youearnedit_samlbase.png)

3. Na **YouEarnedIt domény a adresy URL** části, proveďte následující kroky:

    ![YouEarnedIt domény a adresy URL jednotného přihlašování – informace](./media/youearnedit-tutorial/tutorial_youearnedit_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujících vzorů: 
    | Prostředí  | Vzor  |
    |:--- |:--- |
    | Výroba | `https://<company name>.youearnedit.com/users/sign_in` |
    | Izolovaný prostor  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujících vzorů:
    | Prostředí  | Vzor  |
    |:--- |:--- |
    | Výroba | `https://<company name>.youearnedit.com` |
    | Izolovaný prostor  |`https://<company name>.sandbox.youearnedit.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontaktujte správce úspěchy zákazníků YouEarnedIt přiřazené k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/youearnedit-tutorial/tutorial_youearnedit_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/youearnedit-tutorial/tutorial_general_400.png)

6. Na **YouEarnedIt konfigurace** klikněte na tlačítko **nakonfigurovat YouEarnedIt** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace YouEarnedIt](./media/youearnedit-tutorial/tutorial_youearnedit_configure.png) 

7. Ke konfiguraci jednotného přihlašování na **YouEarnedIt** straně, je nutné odeslat na stažený ***Certificate(Base64)*** a ***SAML jednotné přihlašování – adresa URL služby*** do vaší přiřazené **YouEarnedIt** manažer pro úspěchy zákazníků. Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/youearnedit-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/youearnedit-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/youearnedit-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/youearnedit-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-youearnedit-test-user"></a>Vytvoření zkušebního uživatele YouEarnedIt

V této části vytvoříte uživatele v YouEarnedIt jako Britta Simon. Správce přiřazené YouEarnedIt zákazníkům úspěch a přidejte uživatele na platformě YouEarnedIt prosím pracujete.

>[!NOTE]
>YouEarnedIt očekávat, že zprostředkovatel Identity k poskytování EmailAddress nebo uživatelské jméno v atributu NameID. Ověření se nezdaří, pokud odpovídající uživatelské jméno nebo EmailAddress nebyl nalezen v databázi nebo se neshoduje přesně. To vyžaduje, aby účty importuje i do systému YouEarnedIt před integraci jednotného přihlašování (obvykle buď prostřednictvím rozhraní API nebo CSV import).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k YouEarnedIt použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200]

**Přiřadit YouEarnedIt Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **YouEarnedIt**.

    ![Odkaz YouEarnedIt v seznamu aplikací](./media/youearnedit-tutorial/tutorial_youearnedit_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici YouEarnedIt na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci YouEarnedIt.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/youearnedit-tutorial/tutorial_general_04.png

[100]: ./media/youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/youearnedit-tutorial/tutorial_general_201.png
[202]: ./media/youearnedit-tutorial/tutorial_general_202.png
[203]: ./media/youearnedit-tutorial/tutorial_general_203.png