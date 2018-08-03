---
title: 'Kurz: Integrace Azure Active Directory se službou ITRP | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 0af96b750c7e316d1d394a00781f727358f2c4e8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428311"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Kurz: Integrace Azure Active Directory se službou ITRP

V tomto kurzu se dozvíte, jak integrovat ITRP s Azure Active Directory (Azure AD).

ITRP integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ITRP
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ITRP (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ITRP, potřebujete následující položky:

- S předplatným služby Azure AD
- ITRP jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ITRP z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-itrp-from-the-gallery"></a>Přidání ITRP z Galerie
Konfigurace integrace ITRP ve službě Azure AD, budete muset přidat ITRP z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ITRP z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **ITRP**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/tutorial_itrp_search.png)

1. Na panelu výsledků vyberte **ITRP**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s ITRP podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ITRP je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ITRP potřeba navázat.

V ITRP, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ITRP, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření ITRP testovací uživatele](#creating-an-itrp-test-user)**  – Pokud chcete mít protějšek Britta Simon ITRP, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ITRP.

**Ke konfiguraci Azure AD jednotné přihlašování s ITRP, proveďte následující kroky:**

1. Na webu Azure Portal na **ITRP** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_itrp_samlbase.png)

1. Na **ITRP domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_itrp_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.itrp.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory ITRP klienta](https://www.itrp.com/support) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_itrp_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_general_400.png)

1. Na **ITRP konfigurace** klikněte na tlačítko **nakonfigurovat ITRP** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby a adresu URL odhlašování** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_itrp_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti ITRP jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**.
   
    ![ITRP](./media/itrp-tutorial/ic775570.png "ITRP")

1. V levém navigačním podokně vyberte **Single Sign-On**.
   
    ![Jednotné přihlašování](./media/itrp-tutorial/ic775571.png "jednotného přihlašování")

1. V konfiguračním oddílu Single Sign-On proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/itrp-tutorial/ic775572.png "jednotného přihlašování")
    
    ![Jednotné přihlašování](./media/itrp-tutorial/ic775573.png "jednotného přihlašování")   

    a. Klikněte na **Povolit**.

    b. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    c. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    d.In **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnota certifikátu, který jste zkopírovali z portálu Azure portal. 
      
1. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/itrp-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-itrp-test-user"></a>Vytvoření ITRP testovacího uživatele

Umožňuje uživatelům Azure AD se přihlaste k ITRP, musí být poskytnuty v k ITRP.  

V případě ITRP zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **ITRP** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **záznamy**.
   
    ![Správce](./media/itrp-tutorial/ic775575.png "správce")

1. V místní nabídce vyberte **lidé**.
   
    ![Lidé](./media/itrp-tutorial/ic775587.png "osoby")

1. Klikněte na tlačítko **přidat nové osobě** ("+").
   
    ![Správce](./media/itrp-tutorial/ic775576.png "správce")

1. V dialogovém okně Přidat nové osobě proveďte následující kroky:
   
    ![Uživatel](./media/itrp-tutorial/ic775577.png "uživatele") 
      
    a. Typ **název**, **e-mailu** platného účtu AAD, které chcete zřídit.

    b. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít jakékoli jiné ITRP uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných ITRP uživatelským účtům, zřídit AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ITRP použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit ITRP Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **ITRP**.

    ![Konfigurace jednotného přihlašování](./media/itrp-tutorial/tutorial_itrp_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ITRP na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ITRP.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/itrp-tutorial/tutorial_general_01.png
[2]: ./media/itrp-tutorial/tutorial_general_02.png
[3]: ./media/itrp-tutorial/tutorial_general_03.png
[4]: ./media/itrp-tutorial/tutorial_general_04.png

[100]: ./media/itrp-tutorial/tutorial_general_100.png

[200]: ./media/itrp-tutorial/tutorial_general_200.png
[201]: ./media/itrp-tutorial/tutorial_general_201.png
[202]: ./media/itrp-tutorial/tutorial_general_202.png
[203]: ./media/itrp-tutorial/tutorial_general_203.png

