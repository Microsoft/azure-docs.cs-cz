---
title: 'Kurz: Azure Active Directory integrace s adaptivní Insights | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a adaptivní statistiky.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: jeedes
ms.openlocfilehash: c5ac63bc97924d322ce1a6e734f8ccf21367aafc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35904081"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Kurz: Azure Active Directory integrace s adaptivní statistiky

V tomto kurzu zjistěte, jak integrovat adaptivní Statistika s Azure Active Directory (Azure AD).

Integrace adaptivní Statistika s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k přehledům adaptivní
- Můžete povolit uživatelům, aby automaticky získat přihlášení k adaptivní přehledy (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s adaptivní statistiky, potřebujete následující položky:

- Předplatné služby Azure AD
- Adaptivní Statistika jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání adaptivní Insights z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adaptive-insights-from-the-gallery"></a>Přidání adaptivní Insights z Galerie
Pokud chcete nakonfigurovat integraci adaptivní přehled o Azure AD, potřebujete přidat adaptivní statistiky z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat adaptivní statistiky z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **adaptivní Insights**, vyberte **adaptivní Insights** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s adaptivní přehledy založené na testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v adaptivní přehledy je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v adaptivní přehledy musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s adaptivní Insights, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele adaptivní Insights](#creating-an-adaptive-insights-test-user)**  – Pokud chcete mít protějšek Britta Simon v adaptivní přehledy, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci adaptivní statistiky.

**Ke konfiguraci Azure AD jednotné přihlašování s adaptivní statistiky, proveďte následující kroky:**

1. Na portálu Azure na **adaptivní Insights** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Na **adaptivní statistika domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Identifikátor (Entity ID) a adresa URL odpovědi hodnoty můžete získat z adaptivní Insights **nastavení jednotného přihlašování SAML** stránky.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_general_400.png)

6. Na **adaptivní konfiguraci statistiky** klikněte na tlačítko **konfigurace adaptivní Insights** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se na váš web společnosti adaptivní Insights jako správce.

8. Přejděte na **správce**.

    ![Správce](./media/adaptivesuite-tutorial/IC805644.png "správce")

9. V **uživatelů a rolí** klikněte na tlačítko **spravovat nastavení jednotného přihlašování SAML**.

    ![Spravovat nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/IC805645.png "spravovat nastavení jednotného přihlašování SAML")

10. Na **nastavení jednotného přihlašování SAML** proveďte následující kroky:

    ![Nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/IC805646.png "nastavení jednotného přihlašování SAML")

    a. V **název zprostředkovatele Identity** textovému poli, zadejte název pro svou konfiguraci.

    b. Vložení **SAML Entity ID** hodnota zkopírována z portálu Azure do **zprostředkovatele Identity Entity ID** textové pole.

    c. Vložení **SAML jeden přihlašování adresa URL služby** hodnota zkopírována z portálu Azure do **zprostředkovatele Identity jednotného přihlašování k adrese URL** textové pole.

    d. Vložení **SAML jeden přihlašování adresa URL služby** hodnota zkopírována z portálu Azure do **vlastní adresa URL odhlašovací** textové pole.

    e. Chcete-li nahrát stažený certifikát, klikněte na tlačítko **zvolte soubor**.

    f. Vyberte pro následující:

    * **Id uživatele SAML**, vyberte **adaptivní Statistika uživatelského jména**.

    * **Umístění id uživatele SAML**, vyberte **id uživatele v NameID předmětu**.

    * **Formát SAML NameID**, vyberte **e-mailová adresa**.

    * **Povolit SAML**, vyberte **povolit jednotné přihlašování SAML a přímé přihlášení adaptivní Insights**.

    g. Kopírování **adaptivní přehled jednotného přihlašování k URL** a vložte do **identifikátoru (Entity ID)** a **adresa URL odpovědi** textových polí v **adaptivní statistika domény a adresy URL** části portálu Azure.

    h. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/adaptivesuite-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváření testovacího uživatele Azure AD](./media/adaptivesuite-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.

    ![Vytváření testovacího uživatele Azure AD](./media/adaptivesuite-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Vytváření testovacího uživatele Azure AD](./media/adaptivesuite-tutorial/create_aaduser_04.png)

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-an-adaptive-insights-test-user"></a>Vytváření testovacího uživatele adaptivní statistiky

Pokud chcete povolit uživatelům Azure AD přihlášení ke statistice Adaptivní, musí být zřízená do adaptivní statistiky. V případě adaptivní Statistika zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:** 

1. Přihlaste se k vaší **adaptivní Insights** společnosti lokality jako správce.
2. Přejděte na **správce**.

   ![Správce](./media/adaptivesuite-tutorial/IC805644.png "správce")
3. V **uživatelů a rolí** klikněte na tlačítko **přidat uživatele**.

   ![Přidat uživatele](./media/adaptivesuite-tutorial/IC805648.png "přidat uživatele")
4. V **nového uživatele** část, proveďte následující kroky:

   ![Odeslání](./media/adaptivesuite-tutorial/IC805649.png "odeslání")

   a. Typ **název**, **přihlášení**, **e-mailu**, **heslo** platný Azure Active Directory uživatele, který chcete mají být zahrnuty do související textových polí.

   b. Vyberte **Role**.

   c. Klikněte na tlačítko **odeslání**.

>[!NOTE]
>Můžete použít jakékoli jiné adaptivní Statistika uživatele účtu vytvoření nástroje nebo rozhraní API poskytované adaptivní Statistika zřídit AAD uživatelské účty.
>

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ke adaptivní statistice.

![Přiřadit uživatele][200]

**Pokud chcete přiřadit Britta Simon adaptivní statistiky, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **adaptivní Insights**.

    ![Konfigurovat jednotné přihlašování](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je vyzkoušet Microsoft Azure AD jednotné přihlašování v konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici adaptivní Statistika na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci adaptivní statistiky.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/adaptivesuite-tutorial/tutorial_general_203.png
