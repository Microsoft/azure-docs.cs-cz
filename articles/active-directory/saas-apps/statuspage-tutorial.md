---
title: 'Kurz: Integrace Azure Active Directory s stránka se stavem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a stránka se stavem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: c3532ff5422ae35aa6bb079fcedf3a2a90d45349
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181567"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Kurz: Integrace Azure Active Directory s stránka se stavem

V tomto kurzu se dozvíte, jak integrovat stránka se stavem služby Azure Active Directory (Azure AD).

Stránka se stavem integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k stránka se stavem
- Můžete povolit uživatelům, aby automaticky získat přihlášení k stránka se stavem (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s stránka se stavem, potřebujete následující položky:

- Předplatné Azure AD
- Stránka se stavem jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání stránka se stavem z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-statuspage-from-the-gallery"></a>Přidání stránka se stavem z Galerie
Konfigurace integrace stránka se stavem do služby Azure AD, budete muset přidat stránka se stavem v galerii na váš seznam spravovaných aplikací SaaS.

**Stránka se stavem přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **stránka se stavem**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. Na panelu výsledků vyberte **stránka se stavem**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí stránka se stavem podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v stránka se stavem je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v stránka se stavem.

V stránka se stavem, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s stránka se stavem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele stránka se stavem](#creating-a-statuspage-test-user)**  – Pokud chcete mít protějšek Britta Simon stránka se stavem, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci stránka se stavem.

**Ke konfiguraci Azure AD jednotné přihlašování s stránka se stavem, proveďte následující kroky:**

1. Na webu Azure Portal na **stránka se stavem** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. Na **stránka se stavem domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Obraťte se na tým podpory stránka se stavem v [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)žádat o metadata potřebná ke konfiguraci jednotného přihlašování. 
    >
    >a. Z metadat, zkopírujte hodnotu Issuer a vložte jej do **identifikátor** textového pole.
    >
    >b. Z metadat, zkopírujte adresu URL odpovědi a vložte jej do **adresy URL odpovědi** textového pole.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_general_400.png)

1. Na **stránka se stavem konfigurace** klikněte na tlačítko **stránka se stavem konfigurace** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. V jiném okně prohlížeče Přihlaste se k webu společnosti stránka se stavem jako správce.

1. Na hlavním panelu nástrojů klikněte na tlačítko **spravovat účet**.
   
    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. Klikněte na tlačítko **Single Sign-on** kartu. 
   
    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. V **jednotného přihlašování k cílové adrese URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát** textového pole. 

    c. Klikněte na tlačítko **uložit konfiguraci**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-statuspage-test-user"></a>Vytvoření zkušebního uživatele stránka se stavem

Cílem této části je vytvořte uživatele Britta Simon v stránka se stavem.

Stránka se stavem podporuje zřizování just-in-time. Je už povolená v [konfigurace služby Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

**Vytvořte uživatele v stránka se stavem jako Britta Simon, proveďte následující kroky:**

1. Přihlašování k webu společnosti stránka se stavem jako správce.

1. V nabídce v horní části klikněte na tlačítko **spravovat účet**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na tlačítko **členové týmu** kartu. 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klikněte na tlačítko **člen týmu přidat**. 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platné uživatele, které chcete zřídit do související textových polí. 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **Role**, zvolte **správce klienta**.

1. Klikněte na tlačítko **vytvořit účet**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k stránka se stavem.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit stránka se stavem, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **stránka se stavem**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici stránka se stavem na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci stránka se stavem.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

