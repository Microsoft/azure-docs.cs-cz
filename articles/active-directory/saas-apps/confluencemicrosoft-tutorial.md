---
title: 'Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML soutoku microsoftem | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML soutoku společností Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 2374c90dfde08979bbbf140181c89635c7f798ea
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903314"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML soutoku společností Microsoft

V tomto kurzu zjistěte, jak integrovat jednotné přihlašování SAML soutoku společností Microsoft s Azure Active Directory (Azure AD).

Integrace jednotné přihlašování SAML soutoku společností Microsoft s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k jednotné přihlašování SAML soutoku společností Microsoft
- Můžete povolit uživatelům, aby automaticky získat přihlášení k jednotné přihlašování SAML soutoku společností Microsoft (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Popis:

Pomocí účtu Microsoft Azure Active Directory se serverem Atlassian soutoku umožňující jednotného přihlašování. Tímto způsobem všichni uživatelé vaší organizace slouží k přihlášení do aplikace soutoku přihlašovací údaje Azure AD. Tento modul plug-in používá SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí jednotného přihlašování SAML soutoku společností Microsoft, potřebujete následující položky:

- Předplatné služby Azure AD
- Aplikace serveru soutoku nainstalovaný na 64bitovou verzi Windows serveru (místní nebo v cloudu IaaS infrastrukturu)
- Soutoku server je povolen protokol HTTPS
- Všimněte si, že podporované verze pro modul plug-in soutoku jsou uvedené v následující části.
- Soutoku server je dostupný na Internetu, zvláště na Azure AD přihlašovací stránku pro ověřování a měli schopný přijímat tokenu z Azure AD
- Přihlašovací údaje správce se nastavují v soutoku
- V soutoku je zakázáno WebSudo
- Testovací uživatel vytvořené v aplikaci soutoku server

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme pomocí soutoku provozním prostředí. Nejdřív otestovat integrace v vývoj nebo pracovní prostředí aplikace a pak pomocí produkčního prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Podporované verze systému soutoku 

Od nyní jsou podporovány následující verze soutoku:

- Soutoku: 5.0 pro 5.10

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotné přihlašování SAML soutoku společností Microsoft z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotné přihlašování SAML soutoku společností Microsoft z Galerie
Při konfiguraci integrace přihlašování SAML soutoku společností Microsoft do služby Azure AD, potřebujete přidat jednotné přihlašování SAML soutoku společností Microsoft z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat jednotné přihlašování SAML soutoku společností Microsoft z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML soutoku Microsoft**.

    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Na panelu výsledků vyberte **jednotné přihlašování SAML soutoku Microsoft**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML soutoku společnosti Microsoft podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v jednotné přihlašování SAML soutoku společností Microsoft je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské soutoku SAML SSO společností Microsoft musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML soutoku společností Microsoft, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření jednotné přihlašování SAML soutoku uživatelem testovací Microsoft](#creating-a-confluence-saml-sso-by-microsoft-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML soutoku společnosti Microsoft, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší jednotné přihlašování SAML soutoku aplikací společnosti Microsoft.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML soutoku společností Microsoft, proveďte následující kroky:**

1. Na portálu Azure na **jednotné přihlašování SAML soutoku Microsoft** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. Na **jednotné přihlašování SAML soutoku Microsoft Domain a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<domain:port>/`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Port je volitelný, v případě, že je adresa URL s názvem. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in soutoku, který je vysvětlen později v tomto kurzu.

4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.
    
    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se k instanci soutoku jako správce.

7. Pozastavte ukazatel myši na ikonu a klikněte na **doplňky**.
    
    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/addon1.png)

8. Stáhnout modul plug-in z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56503). Ručně odeslat modulu plug-in poskytnout pomocí Microsoft **nahrát rozšíření** nabídky. Stahování modul plug-in je popsaná v části [servisní smlouvou aplikace Microsoft](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/addon12.png)

9. Po instalaci modulu plug-in se zobrazí v **uživatel nainstaloval** doplňky části **spravovat rozšíření** části. Klikněte na tlačítko **konfigurace** konfigurace nového modulu plug-in.
    
    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/addon13.png)

10. Proveďte následující kroky na stránce konfigurace:

    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Zkontrolujte, zda je namapovaný na aplikaci tak, aby se nezobrazí žádná chyba při řešení metadata jen jeden certifikát. Pokud mají k dispozici víc certifikátů, správce získá chybu při řešení metadata.

    a. V **adresu URL metadat** textovému poli, vložte **adresu Url aplikace federační Metadata** hodnotu, která jste zkopírovali z portálu Azure a klikněte na tlačítko **vyřešit** tlačítko. Přečte adresu URL metadat IdP a naplní všechny informace o pole.

    b. Kopírování **identifikátor, adresa URL odpovědi a adresa URL přihlašování** hodnoty a vložte je do **identifikátor, adresa URL odpovědi a adresa URL přihlašování** textových polí v uvedeném pořadí v **jednotné přihlašování SAML soutoku Microsoft Domain a adresy URL** části na portálu Azure.

    c. V **přihlašovací jméno tlačítko** zadejte název vaší organizace chce, aby se uživatelům zobrazit na přihlašovací obrazovku tlačítka.

    d. V **SAML uživatele ID umístění**, vyberte buď **ID uživatele je v elementu NameIdentifier příkaz subjektu** nebo **ID uživatele je v elementu atribut**.  Toto ID musí být soutoku id uživatele. Pokud neodpovídá id uživatele, systém nedovolí přihlášení uživatelů. 

    > [!Note]
    > Výchozí umístění SAML ID uživatele je identifikátor jména. Můžete to změnit na možnost atribut a zadejte odpovídající název.
    
    e. Pokud vyberete **ID uživatele je v elementu atribut** možnost, pak v **název atributu** textového pole zadejte název atributu, kde je očekávána Id uživatele. 

    f. Pokud používáte federované domény (například služby AD FS atd.) s Azure AD, potom klikněte na **povolit zjišťování domovské sféry** řádku a nakonfigurovat **název domény**.
    
    g. V **název domény** zadejte název domény zde v případě přihlášení na základě služby AD FS.

    h. Zkontrolujte **povolit jednotné přihlašování se** Pokud se chcete odhlásit z Azure AD, když se uživatel neodhlásí z soutoku. 

    i. Klikněte na tlačítko **Uložit** tlačítko pro uložení nastavení.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží najdete v článku [MS soutoku jednotné přihlašování konektor správce průvodce](../ms-confluence-jira-plugin-adminguide.md) a také [– nejčastější dotazy](../ms-confluence-jira-plugin-faq.md) pro vaše pomoc

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Vytváření jednotné přihlašování SAML soutoku Microsoft testovací uživatel

Pokud chcete povolit uživatelům Azure AD přihlášení do soutoku na místním serveru, se musí být zřízená do jednotné přihlašování SAML soutoku společností Microsoft. Pro jednotné přihlašování SAML soutoku společností Microsoft zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší soutoku na místním serveru jako správce.

2. Pozastavte ukazatel myši na ikonu a klikněte na **Správa uživatelů**.

    ![Můžete přidat zaměstnance](./media/confluencemicrosoft-tutorial/user1.png) 

3. V části uživatelé klikněte na tlačítko **přidat uživatele** kartě. Na **přidat uživatele** dialogové okno stránky, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/confluencemicrosoft-tutorial/user2.png) 

    a. V **uživatelské jméno** textovému poli, zadejte e-mailu uživatele jako Britta Simon.

    b. V **úplný název** textovému poli, zadejte úplný název tohoto uživatele jako Britta Simon.

    c. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

    d. V **heslo** textovému poli, zadejte heslo pro Britta Simon.

    e. Klikněte na tlačítko **Potvrdit heslo** znovu zadat heslo.
    
    f. Klikněte na tlačítko **přidat** tlačítko.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu jednotné přihlašování SAML soutoku společností Microsoft.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon jednotné přihlašování SAML soutoku společností Microsoft, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **jednotné přihlašování SAML soutoku Microsoft**.

    ![Konfigurovat jednotné přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko jednotné přihlašování SAML soutoku podle Microsoft dlaždice na přístupovém panelu jste měli získat automaticky přihlášení k vaší jednotné přihlašování SAML soutoku aplikací Microsoft.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/confluencemicrosoft-tutorial/tutorial_general_203.png
