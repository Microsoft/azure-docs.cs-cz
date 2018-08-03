---
title: 'Kurz: Integrace Azure Active Directory s cloudem Lifesize | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lifesize cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c03456dcda2b3ee44686b070cdebb5fc81c3968c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449175"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Kurz: Integrace Azure Active Directory s cloudem Lifesize

V tomto kurzu se dozvíte, jak integrovat Lifesize cloudu s Azure Active Directory (Azure AD).

Integrace Lifesize cloudu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do cloudu Lifesize
- Uživatele, aby automaticky získat přihlášení k Lifesize cloudu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s využitím cloudu Lifesize, potřebujete následující položky:

- S předplatným služby Azure AD
- Cloudu Lifesize jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání cloudové Lifesize z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Přidání cloudové Lifesize z Galerie
Ke konfiguraci integrace Lifesize Cloud do služby Azure AD, budete muset přidat Lifesize Cloud z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Lifesize Cloud z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Lifesize cloudu**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

1. Na panelu výsledků vyberte **Lifesize cloudu**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s využitím cloudu Lifesize podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Lifesize cloudu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v cloudu Lifesize potřeba navázat.

V cloudu Lifesize přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím Lifesize cloudu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Lifesize cloudu](#creating-a-lifesize-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Lifesize cloudu, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Lifesize cloudové aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s využitím cloudu Lifesize, proveďte následující kroky:**

1. Na webu Azure Portal na **Lifesize cloudu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

1. Na **Lifesize Cloudová doména a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.lifesizecloud.com/ls/?acs`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.lifesizecloud.com/<companyname>`

     
1. Zkontrolujte **zobrazit pokročilé nastavení URL**, postupujte následovně:    
   
    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    V **propojení stavu** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Mějte prosím na paměti, že se nejedná skutečné hodnoty. budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresa URL, stav přenosu a identifikátor. Kontakt [tým podpory Lifesize cloudu klienta](https://www.lifesize.com/support) přihlašovací adresa URL a identifikátor hodnoty a hodnotu lze získat stav přenosu z konfigurace jednotného přihlašování, který je vysvětlen později v tomto kurzu.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

1. Na **konfigurace cloudu Lifesize** klikněte na tlačítko **konfigurace cloudu Lifesize** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

1. Chcete-li získat jednotné přihlašování nakonfigurované pro vaši aplikaci, přihlaste se k Lifesize cloudové aplikace s oprávněními správce.

1. V pravém horním rohu klikněte na své jméno a potom klikněte na **rozšířená nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

1. V nastavení zálohy nyní klikněte na **Konfigurace jednotného přihlašování** odkaz. Otevře se stránka Konfigurace jednotného přihlašování pro vaši instanci.
   
    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

1. Teď nakonfigurujte následující hodnoty v konfiguraci jednotného přihlašování uživatelského rozhraní.    
   
    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. V **Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    b.  V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.
  
    d. V mapování atributů SAML pro název prvního textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. V mapování atributů SAML pro **příjmení** textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. V mapování atributů SAML pro **e-mailu** textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

1. Zkontrolujte konfiguraci, můžete kliknout na **Test** tlačítko.
   
    >[!NOTE]
    >Pro úspěšné testování potřebujete k dokončení Průvodce konfigurací ve službě Azure AD a také poskytnout přístup na uživatele nebo skupiny, jež lze provést test.

1. Povolit jednotné přihlašování tak, že zkontrolujete na **povolit jednotné přihlašování** tlačítko.

1. Nyní klikněte na **aktualizace** tlačítko tak, aby všechna nastavení jsou uložena. Tím se vygeneruje hodnota RelayState. Kopírování RelayState hodnoty, který je generován v textovém poli, vložte ji **stav přenosu** textového pole pod **Lifesize Cloudová doména a adresy URL** části. 

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Vytvoření zkušebního uživatele Lifesize cloudu

V této části vytvořte uživatele Britta Simon Lifesize cloudu. Lifesize cloud podporuje automatické zřizování uživatelů. Po úspěšném ověření v Azure AD se automaticky zřídí uživatele v aplikaci. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do cloudu Lifesize.

![Přiřadit uživatele][200] 

**Přiřadit Lifesize Cloud Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Lifesize cloudu**.

    ![Konfigurace jednotného přihlašování](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Lifesize Cloud na přístupovém panelu, měli byste obdržet přihlašovací stránku Lifesize cloudové aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png

