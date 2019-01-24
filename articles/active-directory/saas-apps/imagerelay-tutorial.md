---
title: 'Kurz: Integrace Azure Active Directory s Image Relay | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenosu bitové kopie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0671d2f5ad4be34926c8e3d2b22711c4af5fd435
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812093"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Kurz: Integrace Azure Active Directory s Image Relay

V tomto kurzu se dozvíte, jak integrovat Image Relay s Azure Active Directory (Azure AD).

Obrázek Relay integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k předávání obrázků
- Uživatele, aby automaticky získat přihlášení k předávání obrázků (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Relay bitové kopie, potřebujete následující položky:

- Předplatné Azure AD
- Předávání obrázků jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání propojení Image z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-image-relay-from-the-gallery"></a>Přidání propojení Image z Galerie
Ke konfiguraci integrace služby Image Relay do služby Azure AD, budete muset přidat Image Relay z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Image Relay z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Image Relay**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

1. Na panelu výsledků vyberte **Image Relay**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí bitové kopie přenosu na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Image Relay je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Image Relay je potřeba navázat.

Obrázek Relay, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Relay bitové kopie, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření testovacího uživatele předávání obrázků](#creating-an-image-relay-test-user)**  – Pokud chcete mít protějšek Britta Simon Image Relay, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci bitové kopie Relay.

**Ke konfiguraci Azure AD jednotné přihlašování s Image Relay, postupujte následovně:**

1. Na webu Azure Portal na **Image Relay** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

1. Na **Image Relay domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.imagerelay.com/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Image přenosový klient](http://support.imagerelay.com/) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_general_400.png)

1. Na **konfigurace přenosového Image** klikněte na tlačítko **konfigurace přenosového Image** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresa URL služby a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

1. V jiném okně prohlížeče Přihlaste se na web společnosti Image Relay jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **uživatelů a oprávnění** pracovního vytížení.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Klikněte na tlačítko **vytvořit nové oprávnění**.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

1. V **nastavení jednotného přihlašování** úloh, vyberte **tato skupina může pouze přihlášení pomocí jednotného přihlašování** zaškrtněte políčko a potom klikněte na tlačítko **Uložit**.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Přejděte na **nastavení účtu**.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Přejděte **nastavení jednotného přihlašování** pracovního vytížení.
    
     ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Na **nastavení SAML** dialogového okna, proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    b. V **odhlašovací adresa URL** textového pole vložte hodnotu **adresu URL jednotného odhlašování služby** zkopírovanou z webu Azure portal.

    c. Jako **formát Id názvu**vyberte **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**.

    d. Jako **vazby možnosti pro žádosti od poskytovatele služeb (přenosu Image)** vyberte **příspěvek vazby**.

    e. V části **x.509 Certificate**, klikněte na tlačítko **aktualizace certifikátu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte obsah a vložte ho do textového pole certifikátu x.509.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. V **zřizování uživatelů za běhu** vyberte **povolit zřizování uživatelů za běhu**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vyberte skupinu oprávnění (například **jednotného přihlašování základní**) povolený pro přihlášení jenom prostřednictvím jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-image-relay-test-user"></a>Vytvoření Image Relay testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon Relay bitové kopie.

**K vytvoření uživateli Britta Simon Image Relay, postupujte následovně:**

1. Přihlašování k webu společnosti Image Relay jako správce.

1. Přejděte na **uživatelů a oprávnění** a vyberte **vytvořit jednotné přihlašování uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Zadejte **e-mailu**, **křestní jméno**, **příjmení**, a **společnosti** chcete zřídit a vyberte skupinu oprávnění (pro uživatele Příklad: základní jednotné přihlašování) která je skupina, která mohou přihlašovat pouze pomocí jednotného přihlašování.
   
    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Klikněte na možnost **Vytvořit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k předávání obrázků.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Image Relay, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Image Relay**.

    ![Konfigurace jednotného přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.    

Po kliknutí na dlaždici Relay bitové kopie na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci bitové kopie Relay.


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

