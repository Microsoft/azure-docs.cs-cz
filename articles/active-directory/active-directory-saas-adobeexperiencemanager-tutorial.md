---
title: 'Kurz: Azure Active Directory integrace s nástrojem Adobe prostředí Manager | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe prostředí správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: d63ee16df9b49517c93cdc405cd2525f578d2a7f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Azure Active Directory integrace s nástrojem Adobe prostředí Manager

V tomto kurzu zjistěte, jak integrovat Adobe prostředí správce s Azure Active Directory (Azure AD).

Integrace Adobe prostředí správce s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, kdo má přístup k aplikaci Adobe prostředí Manager.
- Můžete povolit uživatelům automaticky získat přihlášení k aplikaci Adobe prostředí správce s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Adobe prostředí správce, potřebujete následující položky:

- Předplatné služby Azure AD
- Adobe prostředí správce jednotné přihlašování povolené předplatné

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí, zkušební verze Azure AD, [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání správce prostředí Adobe z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Přidat správce prostředí Adobe z Galerie
Konfigurace integrace nástroje Adobe prostředí Manager do Azure AD, potřebujete přidat správce prostředí Adobe z Galerie si na seznam spravovaných aplikací SaaS.

**Přidání správce prostředí Adobe z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Manager prostředí Adobe**. Vyberte **Manager prostředí Adobe** z panelu výsledky a potom vyberte **přidat** tlačítko Přidat aplikaci.

    ![Adobe prostředí správce v seznamu výsledků](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe prostředí Manager na základě testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v aplikaci Adobe prostředí správce je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v aplikaci Adobe prostředí správce.

Ve Správci prostředí Adobe poskytnout hodnota **uživatelské jméno** stejnou hodnotu **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dvěma uživateli. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe prostředí Manager, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvořit uživatele s testovací prostředí správce Adobe](#create-an-adobe-experience-manager-test-user) tak, aby měl protějšek Britta Simon v Adobe prostředí správce, který je propojený s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Adobe prostředí správce.

**Ke konfiguraci Azure AD jednotné přihlašování s Adobe prostředí Manager, proveďte následující kroky:**

1. Na portálu Azure na **Manager prostředí Adobe** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogovém **režimu** rozevírací nabídky vyberte **na základě SAML přihlašování**.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. V **Adobe prostředí správce domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IdP** režimu:

    ![Adobe prostředí správce domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. V **identifikátor** zadejte jedinečnou hodnotu, kterou definujete na vašem AEM serveru. 

    b. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí následujícího vzorce: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem a adresa URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se [tým podpory Adobe prostředí správce](https://helpx.adobe.com/support/experience-manager.html).
 
4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Potom proveďte následující kroky, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Adobe prostředí správce domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL vašeho Adobe prostředí správce serveru. 

5. V **SAML podpisový certifikát** vyberte **certifikátu (Base64)**. Uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Otevřete okno Konfigurace přihlášení v části Konfigurace Adobe prostředí správce, vyberte **konfigurace Manager prostředí Adobe**. Kopírování **SAML adresa URL přihlašování služby**, **SAML Entity ID**, a **Sign-Out ID** z části Stručná referenční příručka.

    ![Odkaz na konfiguraci oddílu](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Vyberte **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. V jiném okně prohlížeče, otevřete **Manager prostředí Adobe** portál pro správu.

9. Vyberte **nastavení** > **zabezpečení** > **uživatelé**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Vyberte **správce** nebo jiné příslušné uživatele.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Vyberte **nastavení účtu** > **spravovat TrustStore**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. V části **přidat certifikát ze souboru CER**, klikněte na tlačítko **vyberte soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z portálu Azure.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certifikát je přidán do TrustStore. Všimněte si alias certifikátu.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Na **uživatelé** vyberte **Služba ověřování**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Vyberte **nastavení účtu** > **vytvořit nebo spravovat úložiště klíčů**. Vytvoření úložiště klíčů zadáním hesla.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Přejděte zpět na obrazovku správce. Potom vyberte **nastavení** > **operace** > **webové konzole**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Otevře se stránka konfigurace.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Najít **obslužná rutina Adobe Granite SAML 2.0 ověřování**. Vyberte **přidat** ikonu.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Na této stránce proveďte následující akce.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. V **cesta** zadejte **/**.

    b. V **IDP URL** zadejte **SAML adresa URL přihlašování služby** hodnotu, kterou jste zkopírovali z portálu Azure.

    c. V **IDP certifikát Alias** zadejte **certifikát Alias** hodnotu, která jste přidali v TrustStore.

    d. V **zabezpečení zadané ID Entity** zadejte jedinečný **SAML Entity ID** hodnotu, která jste nakonfigurovali v portálu Azure.

    e. V **adresa URL služby příjemce Assertion** zadejte **adresa URL odpovědi** hodnotu, která jste nakonfigurovali v portálu Azure.

    f. V **úložiště hesel klíč** zadejte **heslo** nastavený v úložišti klíčů.

    g. V **atribut ID uživatele** zadejte **ID názvu** nebo jiné ID uživatele, které se týkají ve vašem případě.

    h. Vyberte **CRX automaticky vytvořit uživatele**.

    i. V **adresy URL odhlašovací** zadejte jedinečný **Sign-Out URL** hodnotu, která jste získali z portálu Azure.

    j. Vyberte **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě. Přejděte k embedded dokumentace prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, v horní části **všichni uživatelé** dialogové okno, vyberte **přidat**.

    ![Tlačítko Přidat](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtávací políčko. Zapište hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Vytvořit testovací uživatele s Adobe činnost správce

V této části vytvoříte uživatele volal Britta Simon v aplikaci Adobe prostředí správce. Pokud jste vybrali **automaticky vytvořit CRX uživatelé** možnost, uživatelé se vytvoří automaticky po úspěšném ověření. 

Pokud chcete ručně vytvořit uživatele, pracovat [tým podpory Adobe prostředí správce](https://helpx.adobe.com/support/experience-manager.html) přidat uživatele do platformy Adobe prostředí správce. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon tak, že jim udělíte přístup k aplikaci Adobe prostředí Manager používat Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit k aplikaci Adobe prostředí Manager, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace. V dalším kroku, přejděte do adresáře zobrazení, vyberte **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Manager prostředí Adobe**.

    ![Odkaz Adobe prostředí správce v seznamu aplikací](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom v **přidat přiřazení** dialogové okno, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v seznamu uživatelů.

6. V **uživatelů a skupin** dialogové okno, klikněte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždice správce prostředí Adobe na přístupovém panelu, budete by měl získat automaticky přihlášení k aplikaci Adobe prostředí správce.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

