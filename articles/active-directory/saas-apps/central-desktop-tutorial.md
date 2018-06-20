---
title: 'Kurz: Azure Active Directory integrace s centrální plochy | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrální plochy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 4f49d841cc9d6d799b1cd047454acaa2c618af73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211311"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Kurz: Azure Active Directory integrace s centrální plochy

V tomto kurzu zjistěte, jak integrovat centrální plochy s Azure Active Directory (Azure AD).

Integrace centrální plochy s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k centrální plochy.
- Můžete povolit uživatelům automaticky získat přihlášení k ploše centrální s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s centrální Desktop, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné centrální plochy jeden znak na povoleno

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud ještě nemáte zkušební prostředí Azure AD, [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání centrální plochy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-central-desktop-from-the-gallery"></a>Přidat centrální plochy z Galerie
Při konfiguraci integrace centrální plochy do služby Azure AD, potřebujete přidat centrální plochy z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat centrální plochy z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat nové aplikace, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **centrální plochy**. Vyberte **centrální plochy** z panelu výsledky a potom vyberte **přidat** tlačítko Přidat aplikaci.

    ![Centrální plochy v seznamu výsledků](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s centrální Desktop podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v centrální plochy je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v centrální plochy.

V centrální plochy poskytnout **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dvěma uživateli.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s centrální plocha, je třeba dokončit následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele centrální plochy](#create-a-central-desktop-test-user) tak, aby měl protějšek Britta Simon v centrální plochy, která je propojený s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Centrální plochy.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí centrální plochy, proveďte následující kroky:**

1. Na portálu Azure na **centrální plochy** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogovém **režimu** rozevíracího seznamu vyberte **na základě SAML přihlašování**.
 
    ![Jediné přihlášení dialogové okno](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. V **centrální plochy domény a adresy URL** část, proveďte následující kroky:

    ![Centrální plochy domény a adresy URL jednotné přihlašování informace](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí vzoru následující: `https://<companyname>.centraldesktop.com`

    b. V **identifikátor** pole, zadejte adresu URL pomocí vzoru následující:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí vzoru následující: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem odpovědět adresu URL a přihlašovací adresa URL. Obraťte se [tým podpory klienta centrální plochy](https://imeetcentral.com/contact-us) k získání těchto hodnot. 

4. V **SAML podpisový certifikát** vyberte **certifikát**. Uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/central-desktop-tutorial/tutorial_general_400.png)
    
6. V **centrální konfigurace plochy** vyberte **nakonfigurovat centrální plochy** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka** části.

    ![Konfigurace centrálního plochy](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Přihlaste se k vaší **centrální plochy** klienta.

8. Přejděte na **nastavení**. Vyberte **Upřesnit**a potom vyberte **jednotné přihlašování**.

    ![Instalační program – pokročilé](./media/central-desktop-tutorial/ic769563.png "instalační program – Rozšířená")

9. Na **jeden znak v nastavení** proveďte následující kroky:

    ![Jednotné přihlašování v nastavení](./media/central-desktop-tutorial/ic769564.png "jeden znak v nastavení")
    
    a. Vyberte **povolit SAML v2 jednotného přihlašování**.
    
    b. V **jednotného přihlašování k adrese URL** pole, vložte **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure.
    
    c. V **adresu URL pro přihlášení SSO** pole, vložte **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure.
    
    d. V **adresy URL odhlašovací jednotného přihlašování k** pole, vložte **Sign-Out URL** hodnotu, kterou jste zkopírovali z portálu Azure.

10. V **metodu ověřování podpisu zpráva** část, proveďte následující kroky:

    ![Zpráva metodu ověřování podpisu](./media/central-desktop-tutorial/ic769565.png "metodu ověřování podpisu zpráva") . Vyberte **Certifikát**.
    
    b. V **certifikát jednotného přihlašování k** seznamu, vyberte **RSH SHA256**.
    
    c. V poznámkovém bloku otevřete stažený certifikát. Pak zkopírujte obsah certifikát a vložte ji do **certifikát jednotného přihlašování k** pole.
        
    d. Vyberte **zobrazí odkaz na přihlašovací stránku vaší SAMLv2**.
    
    e. Vyberte **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete embedded dokumentace prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/central-desktop-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/central-desktop-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/central-desktop-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-central-desktop-test-user"></a>Vytvoření zkušebního uživatele centrální plochy

Azure AD uživatelé moci přihlásit musí být zřízená v aplikaci Centrální plochy. Tato část popisuje, jak vytvořit uživatelské účty Azure AD v centrální plochy.

> [!NOTE]
> Ke zřízení uživatelských účtů Azure AD, můžete použít jiné nástroje pro tvorbu centrální plochy uživatele účtu nebo rozhraní API, které jsou poskytovány centrální plochy.

**Ke zřízení uživatelských účtů do centrální plochy:**

1. Přihlaste se ke klientovi centrální plochy.

2. Přejděte na **osoby** > **vnitřní členy**.

3. Vyberte **přidat vnitřní členy**.

    ![Lidé](./media/central-desktop-tutorial/ic781051.png "osoby")
    
4. V **e-mailovou adresu nové členy** zadejte účet služby Azure AD, které chcete zřídit a potom vyberte **Další**.

    ![E-mailové adresy nové členy](./media/central-desktop-tutorial/ic781052.png "e-mailové adresy nové členy")

5. Vyberte **přidejte interní členy**.

    ![Přidat vnitřní člen](./media/central-desktop-tutorial/ic781053.png "přidat vnitřní člen")
   
   >[!NOTE]
   >Uživatelé, které přidáte obdrží e-mail, který obsahuje odkaz potvrzení pro aktivaci své účty.
   
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat Azure jednotné přihlašování, protože jim přístup k ploše centrální.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon centrální plochy, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace. Přejděte do zobrazení adresáře a potom přejděte na **podnikové aplikace, které**.

2. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **centrální plochy**.

    ![V seznamu aplikací na odkaz centrální plochy](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v **uživatelé** seznamu.

6. V **uživatelů a skupin** dialogové okno, klikněte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestujte pomocí přístupového panelu Azure AD jeden přihlašování v konfiguraci.

Když vyberete dlaždici centrální plochy na přístupovém panelu, budete automaticky získat přihlášení k aplikaci Centrální plochy.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

