---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML Confluence microsoftem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML Confluence společností Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 97b6507cb8c5a66b8b6ccbfccd2c838b4b3fb53a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053297"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML Confluence společností Microsoft

V tomto kurzu se dozvíte, jak integrovat Confluence SAML SSO, Microsoft Azure Active Directory (Azure AD).

Integrace jednotného přihlašování SAML Confluence společností Microsoft s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Confluence SAML SSO společností Microsoft
- Uživatele, aby automaticky získat přihlášení k Confluence SAML SSO společností Microsoft (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Popis:

Pomocí účtu Microsoft Azure Active Directory se od společnosti Atlassian Confluence serverem můžete povolit jednotné přihlašování. Tímto způsobem všichni uživatelé vaší organizace můžete použít přihlašovací údaje služby Azure AD k přihlášení do aplikace Confluence. Tento modul plug-in používá protokol SAML 2.0 pro federaci.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML Confluence microsoftem, potřebujete následující položky:

- S předplatným služby Azure AD
- Confluence serverová aplikace nainstalované na serveru Windows 64-bit (místní nebo v cloudové infrastruktuře IaaS)
- Confluence server je povolen protokol HTTPS
- Všimněte si, že podporované verze pro modul plug-in Confluence jsou uvedené v následující části.
- Confluence server je dostupný na Internetu zejména na stránku pro přihlášení k Azure AD pro ověřování a měli byste může přijímat token ze služby Azure AD
- Přihlašovací údaje správce jsou nastavené v Confluence
- WebSudo zakázaná v Confluence
- V serveru aplikaci Confluence testovacího uživatele

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkčního prostředí Confluence. Nejdřív otestovat integraci v vývojové nebo testovací prostředí aplikace a pak používat produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Podporované verze systémů Confluence 

V současné době jsou podporovány následující verze Confluence:

- Confluence: 5.0 k 5.10

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování SAML Confluence společností Microsoft z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Přidání jednotného přihlašování SAML Confluence společností Microsoft z Galerie
Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML Confluence microsoftem do služby Azure AD, budete muset přidat Confluence SAML SSO společností Microsoft z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání jednotného přihlašování SAML Confluence společností Microsoft z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Confluence SAML SSO microsoftem**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Na panelu výsledků vyberte **Confluence SAML SSO microsoftem**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence společností Microsoft na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Confluence SAML SSO společností Microsoft je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Confluence SAML SSO společností Microsoft.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence microsoftem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření jednotné přihlašování SAML Confluence uživatelem Microsoft test](#creating-a-confluence-saml-sso-by-microsoft-test-user)**  – Pokud chcete mít protějšek Britta Simon Confluence SAML SSO společnosti Microsoft, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich Confluence SAML SSO aplikací společnosti Microsoft.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Confluence microsoftem, proveďte následující kroky:**

1. Na webu Azure Portal na **Confluence SAML SSO microsoftem** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. Na **Confluence SAML SSO Microsoft Domain a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Port je volitelný, v případě, že je pojmenovaný URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in Confluence, který je vysvětlen později v tomto kurzu.

4. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.
    
    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/tutorial_general_400.png)

6. V okně jiné webové prohlížeče Přihlaste se k vaší instanci Confluence jako správce.

7. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.
    
    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon1.png)

8. Stáhněte si modul plug-in z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56503). Ručně nahrát modul plug-in poskytl pomocí Microsoft **nahrát doplněk** nabídky. Stáhnout modul plug-in se vztahuje smlouva [servisní smlouvou aplikace Microsoft](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon12.png)

9. Když je nainstalovaný modul plug-in, zobrazí se v **uživatel nainstaloval** doplňky část **spravovat doplněk** části. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.
    
    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon13.png)

10. Proveďte následující kroky na stránce konfigurace:

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Ujistěte se, že je pouze jeden certifikát mapují na aplikaci tak, aby se nezobrazí žádná chyba překladu metadat. Pokud existuje víc certifikátů, správce získá chybu při překladu metadat.

    a. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z webu Azure portal a klikněte **vyřešit** tlačítko. Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

    b. Kopírovat **identifikátor, adresa URL odpovědi a adresa URL přihlašování** hodnoty a vložit je do **identifikátor, adresa URL odpovědi a adresa URL přihlašování** textová pole v uvedeném pořadí **Confluence SAML SSO Microsoft Domain a adresy URL**  části na webu Azure portal.

    c. V **přihlašovací jméno tlačítko** zadejte název tlačítka, které vaše organizace chce, aby se uživatelům zobrazí na obrazovce přihlášení.

    d. V **SAML uživatelské ID umístění**, vyberte buď **ID uživatele není v elementu NameIdentifier příkazu subjektu** nebo **ID uživatele není v elementu atribut**.  Toto ID musí být Confluence id uživatele. Pokud neodpovídá id uživatele, systém nedovolí uživatelům přihlášení. 

    > [!Note]
    > Výchozí umístění SAML ID uživatele je název identifikátoru. Můžete to změnit atribut možnost a zadejte odpovídající název.
    
    e. Pokud vyberete **ID uživatele není v elementu atribut** možnost, pak v **název atributu** textového pole zadejte název atributu, kde se očekává Id uživatele. 

    f. Pokud používáte federovanou doménu (například služby AD FS atd.) pomocí služby Azure AD, potom klikněte na **povolit zjišťování domovské sféry** řádku a nakonfigurovat **název domény**.
    
    g. V **název domény** zadejte název domény zde v případě přihlášení na základě služby AD FS.

    h. Zkontrolujte **povolit jednotné přihlašování,** Pokud se chcete odhlásit z Azure AD, když uživatel odhlásí od Confluence. 

    i. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    > [!NOTE]
    > Další informace o instalaci a řešení potíží s [příručky pro správce konektoru MS Confluence jednotného přihlašování](../ms-confluence-jira-plugin-adminguide.md) a k dispozici je také [nejčastější dotazy k](../ms-confluence-jira-plugin-faq.md) vaši pomoc

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Vytváří se jednotné přihlašování Confluence SAML podle Microsoft testovacího uživatele

Umožňuje uživatelům Azure AD se přihlaste k Confluence na místním serveru, musí být zřízená do Confluence SAML SSO společností Microsoft. Zřizování je pro jednotné přihlašování SAML Confluence microsoftem, úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k serveru v místním Confluence jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/confluencemicrosoft-tutorial/user1.png) 

3. V sekci uživatelé klikněte na možnost **přidat uživatele** kartu. Na **přidání uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/confluencemicrosoft-tutorial/user2.png) 

    a. V **uživatelské jméno** textového pole zadejte e-mailu uživatele, jako je Britta Simon.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo pro Britta Simon.

    e. Klikněte na tlačítko **potvrzení hesla** znovu zadat heslo.
    
    f. Klikněte na tlačítko **přidat** tlačítko.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Confluence SAML SSO společností Microsoft.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Confluence SAML SSO microsoftem, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Confluence SAML SSO microsoftem**.

    ![Konfigurace jednotného přihlašování](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Confluence SAML SSO podle Microsoft dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší Confluence SAML SSO aplikací společnosti Microsoft.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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
