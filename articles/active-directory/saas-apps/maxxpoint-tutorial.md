---
title: 'Kurz: Integrace Azure Active Directory s MaxxPoint | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MaxxPoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ba026e-96fc-4ae8-b135-0169da810e99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23801a796473d7985c17ffcf8a9f1350c1b0e8e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187759"
---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Kurz: Integrace Azure Active Directory s MaxxPoint

V tomto kurzu se dozvíte, jak integrovat MaxxPoint s Azure Active Directory (Azure AD).

MaxxPoint integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k MaxxPoint
- Můžete povolit uživatelům, aby automaticky získat přihlášení k MaxxPoint (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s MaxxPoint, potřebujete následující položky:

- Předplatné Azure AD
- MaxxPoint jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání MaxxPoint z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-maxxpoint-from-the-gallery"></a>Přidání MaxxPoint z Galerie
Konfigurace integrace MaxxPoint do služby Azure AD, budete muset přidat MaxxPoint z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat MaxxPoint z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna Přidat novou aplikaci.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **MaxxPoint**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_001.png)

1. Na panelu výsledků vyberte **MaxxPoint**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí MaxxPoint podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v MaxxPoint je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MaxxPoint potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v MaxxPoint.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s MaxxPoint, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele MaxxPoint](#creating-a-maxxpoint-test-user)**  – Pokud chcete mít protějšek Britta Simon MaxxPoint, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci MaxxPoint.

**Ke konfiguraci Azure AD jednotné přihlašování s MaxxPoint, proveďte následující kroky:**

1. Na webu Azure Portal na **MaxxPoint** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_general_300.png)

1. Na **MaxxPoint domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**, nemusíte provádět žádné kroky.

    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
1. Na **MaxxPoint domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_03.png)

    a. Klikněte na tlačítko **zobrazit pokročilé nastavení URL** možnost

    b. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`

    > [!NOTE] 
    > Všimněte si, že to není skutečné hodnoty. Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Volání MaxxPoint týmu na **888-728-0950** tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_general_400.png)

1. Chcete-li získat jednotné přihlašování nakonfigurované pro vaši aplikaci, zavolejte MaxxPoint tým podpory na **888-728-0950** a budete vám dál pomůžou o tom, jak je zadat na stažený **soubor XML s metadaty** souboru. 

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** jednoduše klikněte na tlačítko **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/maxxpoint-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-maxxpoint-test-user"></a>Vytvoření zkušebního uživatele MaxxPoint

V této části vytvoříte uživatele v MaxxPoint jako Britta Simon. Zavolejte na tým podpory MaxxPoint **888-728-0950** přidat uživatele v aplikaci MaxxPoint.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k MaxxPoint.

![Přiřadit uživatele][200] 

**Přiřadit MaxxPoint Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **MaxxPoint**.

    ![Konfigurace jednotného přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici MaxxPoint na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci MaxxPoint.


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/maxxpoint-tutorial/tutorial_general_203.png
