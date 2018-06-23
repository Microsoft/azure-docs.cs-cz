---
title: 'Kurz: Azure Active Directory integrace s iPass SmartConnect | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 5ae600142f7e90a7f6185c3a948a4174ce4c7797
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320524"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Kurz: Azure Active Directory integrace s iPass SmartConnect

V tomto kurzu zjistěte, jak integrovat iPass SmartConnect s Azure Active Directory (Azure AD).

Integrace iPass SmartConnect s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k iPass SmartConnect.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iPass SmartConnect (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iPass SmartConnect, potřebujete následující položky:

- Předplatné služby Azure AD
- IPass SmartConnect jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iPass SmartConnect z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Přidání iPass SmartConnect z Galerie
Při konfiguraci integrace iPass SmartConnect do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS iPass SmartConnect z galerie.

**Pokud chcete přidat iPass SmartConnect z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **iPass SmartConnect**, vyberte **iPass SmartConnect** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![iPass SmartConnect v seznamu výsledků](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s iPass SmartConnect založené na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v iPass SmartConnect je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v iPass SmartConnect musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iPass SmartConnect, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit uživatele s iPass SmartConnect test](#create-an-ipass-smartconnect-test-user)**  – Pokud chcete mít protějšek Britta Simon v iPass SmartConnect propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaše iPass SmartConnect aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s iPass SmartConnect, proveďte následující kroky:**

1. Na portálu Azure na **iPass SmartConnect** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

3. Na **iPass SmartConnect domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** initiated režimu, není nutné provádět žádné kroky.

    ![jednotné přihlašování informace iPass SmartConnect domény a adresy URL](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

4. Zkontrolujte zobrazit upřesňující nastavení adresy URL a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![jednotné přihlašování informace iPass SmartConnect domény a adresy URL](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Do textového pole Přihlašovací adresa URL zadejte adresu URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

5. iPass SmartConnect aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování](./media/ipasssmartconnect-tutorial/attribute.png)

6. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtnout políčko **uživatelské atributy** rozbalte atributy. Proveďte následující kroky na každém z zobrazených atributů-

    | Název atributu | Hodnota atributu | Hodnota Namespace|
    | ---------------| --------------- |----------------|
    | FirstName | user.givenname |   |
    | Příjmení | user.surname | |
    | e-mail | user.userprincipalname | |
    | uživatelské jméno | user.userprincipalname | |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Hodnota oboru názvů zachovat prázdná pro tento řádek.

    e. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

9. Konfigurace jednotného přihlašování na **iPass SmartConnect** straně, budete muset odeslat stažené **soubor XML s metadaty** a **název domény** k [iPass SmartConnect tým podpory](mailto:help@ipass.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Vytvořit uživatele s iPass SmartConnect testu

V této části vytvoříte volal Britta Simon v iPass SmartConnect uživatele. Práce s [tým podpory iPass SmartConnect](mailto:help@ipass.com) přidat uživatele nebo doménu, která je potřeba na seznam povolených adres v platformě SmartConnect iPass. Pokud doména je přidána tým, se získat uživatelé automaticky zřizovat iPass SmartConnect platformě. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k iPass SmartConnect.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon iPass SmartConnect, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **iPass SmartConnect**.

    ![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

**K testování aplikace v toku iniciované SP, proveďte následující kroky:**

a. Stáhněte si windows iPass SmartConnect klienta [zde](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing3.png)

b. Nainstalujte klienta a spuštění.

c. Klikněte na **Začínáme**.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Zadejte název Azure uživatele s doménou. Klikněte na **pokračovat**. To bude přesměrován na stránku přihlášení k Azure

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Po úspěšném ověření bude spuštěn aktivace klienta. Klient bude získat aktivován.

**K testování aplikace v toku IdP spustil, proveďte následující kroky:**

a. Přihlášení k [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klikněte na iPass SmartConnect aplikace.

c. Spustí SSA stránky, klikněte na **stáhnout aplikaci pro Windows** k instalaci iPass SmartConnect klienta.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing4.png)

d. Po instalaci klienta na prvním spuštění se automaticky spustí po přijetí podmínek a ujednání aktivace.

e. Pokud se aktivace nepodaří spustit, klikněte na tlačítko aktivovat na stránce SSA zahájí aktivaci.

f. Klient bude získat aktivován.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

