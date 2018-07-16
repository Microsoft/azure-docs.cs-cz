---
title: 'Kurz: Integrace Azure Active Directory s Adobe Experience Manager | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 56b392e57809cea0ae93800df39bb9dacd164ce2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054178"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Integrace Azure Active Directory s Adobe Experience Manager

V tomto kurzu se dozvíte, jak integrovat Adobe Experience Manager se službou Azure Active Directory (Azure AD).

Integrace s Azure AD Adobe Experience Manager poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k aplikaci Adobe Experience Manager.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Adobe Experience Manager pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Experience Manager, potřebujete následující položky:

- S předplatným služby Azure AD
- Adobe Experience Manager jednotné přihlašování povoleno předplatné

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud nemáte Azure AD zkušební prostředí [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe Experience Manager z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Adobe Experience Manager přidat z Galerie
Konfigurace integrace nástroje Adobe Experience Manager do služby Azure AD, budete muset přidat Adobe Experience Manager na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Adobe Experience Manager z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Adobe Experience Manager**. Vyberte **Adobe Experience Manager** z panel výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Adobe Experience Manager v seznamu výsledků](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotného přihlašování se podle testu uživateli "Britta Simon." programem Adobe Experience Manager

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo protějšek uživatele v aplikaci Adobe Experience Manager je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a souvisejících uživatele v aplikaci Adobe Experience Manager.

V aplikaci Adobe Experience Manager poskytovala hodnotu **uživatelské jméno** stejnou hodnotu **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Experience Manager, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvořit testovacího uživatele Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) mít protějšek Britta Simon v Adobe Experience Manager, který je propojený s Azure AD reprezentace uživatele.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#test-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Adobe Experience Manager.

**Ke konfiguraci Azure AD jednotné přihlašování s Adobe Experience Manager, proveďte následující kroky:**

1. Na webu Azure Portal na **Adobe Experience Manager** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogu **režimu** rozevírací nabídky vyberte **přihlašování na základě SAML**.
 
    ![Jednotné přihlašování – dialogové okno](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. V **Adobe Experience Manager domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IdP** režimu:

    ![Adobe Experience Manager domény a adresy URL jednotného přihlašování – informace](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. V **identifikátor** zadejte jedinečnou hodnotu, která jsou definovány na serveru nástroje AEM. 

    b. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. K získání těchto hodnot, obraťte se [tým podpory Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Zkontrolujte **zobrazit pokročilé nastavení URL**. Pak proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Adobe Experience Manager domény a adresy URL jednotného přihlašování – informace](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL serveru Adobe Experience Manager. 

5. V **podpisový certifikát SAML** vyberte **certifikát (Base64)**. Uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. V části Konfigurace Adobe Experience Manager otevřete okno Konfigurace přihlašování, vyberte **konfigurace Adobe Experience Manager**. Kopírovat **URL služby přihlašování SAML**, **SAML Entity ID**, a **odhlašování ID** z části Stručná referenční příručka.

    ![Konfigurační oddíl odkaz](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Vyberte **Uložit**.

    ![Konfigurace jednotného přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. V jiném okně prohlížeče, otevřete **Adobe Experience Manager** portál pro správu.

9. Vyberte **nastavení** > **zabezpečení** > **uživatelé**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Vyberte **správce** nebo jiné příslušné uživatele.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Vyberte **nastavení účtu** > **spravovat TrustStore**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. V části **přidat certifikát ze souboru CER**, klikněte na tlačítko **vyberte soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z webu Azure portal.

    ![Konfigurace jednotného přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certifikát je přidán do TrustStore. Poznámka: alias certifikátu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na **uživatelé** stránce **ověřovací službu**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Vyberte **nastavení účtu** > **vytvoření/Správa KeyStore**. Vytvořte úložiště klíčů zadáním hesla.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Přejděte zpět na obrazovku pro správu. Potom vyberte **nastavení** > **operace** > **Webová konzola**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Otevře se stránka konfigurace.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Najít **obslužná rutina Adobe Granite SAML 2.0 ověřování**. Vyberte **přidat** ikonu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Pomocí následujících kroků na této stránce.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. V **cesta** zadejte **/**.

    b. V **adresa URL zprostředkovatele identity** zadejte **URL služby přihlašování SAML** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **Alias certifikátu zprostředkovatele identity** zadejte **Alias certifikátu** hodnotu, která jste přidali v TrustStore.

    d. V **zabezpečení zadané ID Entity** zadejte jedinečné **SAML Entity ID** hodnotu, která jste nakonfigurovali na webu Azure Portal.

    e. V **adresa URL služby Assertion příjemce** zadejte **adresy URL odpovědi** hodnotu, která jste nakonfigurovali na webu Azure Portal.

    f. V **heslo klíče Store** zadejte **heslo** , kterou jste nastavili v úložišti klíčů.

    g. V **atribut ID uživatele** zadejte **ID názvu** nebo jiné ID uživatele, který je na váš případ nevztahuje.

    h. Vyberte **uživatele automaticky vytvořit CRX**.

    i. V **odhlašovací adresa URL** zadejte jedinečné **odhlašování URL** hodnoty, které jste získali z portálu Azure portal.

    j. Vyberte **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu. Přejděte k vložená dokumentace prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace na [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, v horní části **všichni uživatelé** dialogu **přidat**.

    ![Tlačítko Přidat](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Vytvoření Adobe Experience Manager testovacího uživatele

V této části vytvoříte uživateli Britta Simon v aplikaci Adobe Experience Manager. Pokud jste vybrali **uživatele automaticky vytvořit CRX** možnost, uživatelé se vytvoří automaticky po úspěšném ověření. 

Pokud chcete ručně vytvořit uživatele, pracovat [tým podpory Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) přidat uživatele na platformě Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že jim udělíte přístup k aplikaci Adobe Experience Manager.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k Adobe Experience Manager Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Dále přejděte do adresáře zobrazení, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Adobe Experience Manager**.

    ![Adobe Experience Manager odkaz v seznamu aplikací](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů.

6. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici Adobe Experience Manager na přístupovém panelu, vám by měl získat automaticky přihlášeni k aplikaci Adobe Experience Manager.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

