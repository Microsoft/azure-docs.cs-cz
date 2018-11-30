---
title: 'Kurz: Integrace Azure Active Directory se službou odchylek | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a odchylek.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: c08b57c41cc43bfa47dee69a75663e7a58ef4c21
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632836"
---
# <a name="tutorial-azure-active-directory-integration-with-drift"></a>Kurz: Integrace Azure Active Directory se službou odchylek

V tomto kurzu se dozvíte, jak integrovat odchylek s Azure Active Directory (Azure AD).

Integrace odchylek s využitím Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k odchylek.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k odchylek (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s odchylek, potřebujete následující položky:

- Předplatné Azure AD
- Odchylek jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkční prostředí, nepoužívejte, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání odchylek z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-drift-from-the-gallery"></a>Přidání odchylek z Galerie

Konfigurace integrace odchylek do služby Azure AD, budete muset přidat odchylek z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat odchylek z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **odchylek**vyberte **odchylek** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Odchylek v seznamu výsledků](./media/drift-tutorial/tutorial_drift_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí odchylek podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v odchylek je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v odchylek.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s odchylek, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele odchylek](#creating-a-drift-test-user)**  – Pokud chcete mít protějšek Britta Simon odchylek, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci odchylek.

**Ke konfiguraci Azure AD jednotné přihlašování s odchylek, proveďte následující kroky:**

1. Na webu Azure Portal na **odchylek** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Odchylek domény a adresy URL jednotného přihlašování – informace](./media/drift-tutorial/tutorial_drift_url.png)

    a. Klikněte na tlačítko **nastavit další adresy URL**.

    b. V **stav přenosu** textové pole, zadejte adresu URL: `https://app.drift.com`

    c. Pokud chcete nakonfigurovat aplikace v **SP** zahájeno v režimu **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://start.drift.com`

5. Použití odchylek očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![image](./media/drift-tutorial/tutorial_drift_attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele a deklarace identity** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | Název | user.displayname |

    a. Klikněte na `name` deklarace identity (zvýrazněná deklarace identity) Chcete-li otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/drift-tutorial/tutorial_drift_attribute1.png)

    ![image](./media/drift-tutorial/tutorial_drift_attribute3.png)

    b. Vyberte zdroj jako **atribut**.

    c. Ujistěte se, **Namespace** prázdné.

    d. Z **zdrojový atribut** seznamu vyberte **user.displayname**.

    e. Klikněte na **Uložit**.

7. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/drift-tutorial/tutorial_drift_certificate.png) 

8. V okně jiné webové prohlížeče, přihlaste se k odchylek jako správce.

9. V levé části řádku nabídek klikněte na **ikona nastavení** > **nastavení aplikace** > **ověřování** a proveďte následující kroky:

    ![Správce odkazů](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Nahrát **kód XML metadat federace** , který jste si stáhli z portálu Azure portal do **zprostředkovatele Identity nahrát soubor metadat** textového pole.

    b. Po nahrání souboru metadat, zbývající hodnoty získat automaticky vyplní automaticky na stránce.

    c. Klikněte na tlačítko **povolit SAML**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-drift-test-user"></a>Vytvoření zkušebního uživatele odchylek

Cílem této části je vytvořte uživatele Britta Simon v odchylek. Odchylek podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k odchylek, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory odchylek](mailto:integrations@drift.com).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k odchylek.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **odchylek**.

    ![Konfigurace jednotného přihlašování](./media/drift-tutorial/tutorial_drift_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení**, dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici odchylek na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci odchylek.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
