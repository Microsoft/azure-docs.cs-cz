---
title: 'Kurz: Integrace Azure Active Directory s Five9 Plus adaptérem (CTI, agenti System Center kontakt) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Five9 Plus adaptérem (CTI, agenti System Center kontakt).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: 8ee04008b62867c8eba68b1525cf50edec881cbc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432629"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Integrace Azure Active Directory s Five9 Plus adaptérem (CTI, agenti System Center kontakt)

V tomto kurzu se dozvíte, jak integrovat Five9 Plus adaptér (CTI, kontaktujte agenty System Center) se službou Azure Active Directory (Azure AD).

Integrace Five9 Plus adaptér (CTI, agenti System Center kontakt) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Five9 Plus adaptér (CTI, agenti System Center kontakt)
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Five9 Plus adaptéru (CTI, agenti System Center kontakt) (jednotné přihlašování) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), potřebujete následující položky:

- S předplatným služby Azure AD
- Five9 Plus adaptér (CTI, agenti System Center kontakt) jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Five9 Plus adaptér (CTI, agenti System Center kontakt) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidání Five9 Plus adaptér (CTI, agenti System Center kontakt) z Galerie
Ke konfiguraci integrace Five9 Plus adaptér (CTI, agenti System Center kontakt) do služby Azure AD, musíte přidat Five9 Plus adaptér (CTI, agenti System Center kontakt) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Five9 Plus adaptér (CTI, agenti System Center kontakt) z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Five9 Plus adaptér (CTI, agenti System Center kontakt)**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/tutorial_five9_search.png)

1. Na panelu výsledků vyberte **Five9 Plus adaptér (CTI, agenti System Center kontakt)** a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Five9 Plus adaptér (CTI, agenti System Center kontakt) na základě testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Five9 Plus adaptérem (CTI, agenti System Center kontakt) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Five9 Plus adaptérem (CTI, agenti System Center kontakt).

V Five9 Plus adaptéru (CTI, agenti System Center kontaktu), přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Five9 Plus adaptér (CTI, agenti System Center kontakt)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  – Pokud chcete mít protějšek Britta Simon Five9 Plus adaptérem (CTI, agenti System Center kontaktu), který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Five9 Plus adaptér (CTI, agenti System Center kontakt).

**Ke konfiguraci Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, agenti System Center kontaktu), proveďte následující kroky:**

1. Na webu Azure Portal na **Five9 Plus adaptér (CTI, agenti System Center kontakt)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. Na **Five9 Plus adaptér (CTI, agenti System Center kontakt) domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujících vzorů:

    |    Prostředí      |       zprostředkovatele identity      |
    | :-- | :-- |
    | Za "Five9 i adaptér pro Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Za "Five9 i adaptér pro Zendesku." | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Za "Five9 i adaptér pro klasické pracovní plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    |      Prostředí     |      zprostředkovatele identity      |
    | :--                  | :--           |
    | Za "Five9 i adaptér pro Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Za "Five9 i adaptér pro Zendesku." | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Za "Five9 i adaptér pro klasické pracovní plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_general_400.png)

1. Na **konfigurace Five9 Plus adaptéru (CTI, agenti System Center kontakt)** klikněte na tlačítko **konfigurace Five9 Plus adaptér (CTI, agenti System Center kontakt)** otevřete **nakonfigurovatpřihlašování**okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_five9_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Five9 Plus adaptér (CTI, agenti System Center kontakt)** straně, je nutné odeslat na stažený **Certificate(Base64), adresa URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby**k [Five9 Plus (CTI, agenti System Center kontakt) podporu adaptérů](https://www.five9.com/about/contact). Kromě toho také pro další konfiguraci jednotného přihlašování postupujte prosím podle níže uvedený postup podle adaptér:

    a. "Five9 Plus adaptér pro klasické pracovní plochy Toolkit agenta" příručky pro správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adaptér pro Microsoft Dynamics CRM" příručky pro správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adaptér pro Zendesk" příručky pro správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/five9-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvoření zkušebního uživatele Five9 Plus adaptér (CTI, agenti System Center kontakt)

V této části vytvořte uživatele Britta Simon Five9 Plus adaptérem (CTI, agenti System Center kontakt). Práce s [Five9 Plus (CTI, agenti System Center kontakt) podporu adaptérů](https://www.five9.com/about/contact) přidat uživatele na platformě Five9 Plus adaptér (CTI, agenti System Center kontakt). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Five9 Plus adaptéru (CTI, agenti System Center kontakt).

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Five9 Plus adaptér (CTI, agenti System Center kontaktu), proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Five9 Plus adaptér (CTI, agenti System Center kontakt)**.

    ![Konfigurace jednotného přihlašování](./media/five9-tutorial/tutorial_five9_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Five9 Plus adaptér (CTI, agenti System Center kontakt) na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Five9 Plus adaptér (CTI, agenti System Center kontakt).
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

