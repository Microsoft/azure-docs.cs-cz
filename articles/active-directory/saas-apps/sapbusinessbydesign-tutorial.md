---
title: 'Kurz: Azure Active Directory integrace s SAP Business ByDesign | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 446149e21bb5fff69df26b567469e856e245da16
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215469"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign

V tomto kurzu zjistěte, jak integrovat SAP Business ByDesign s Azure Active Directory (Azure AD).

Integrace SAP Business ByDesign s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SAP Business ByDesign.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k prostředí SAP Business ByDesign (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP Business ByDesign, potřebujete následující položky:

- Předplatné služby Azure AD
- SAP Business ByDesign jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP Business ByDesign z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Přidání SAP Business ByDesign z Galerie
Konfigurace integrace ByDesign obchodní SAP do Azure AD, potřebujete přidat SAP Business ByDesign z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SAP Business ByDesign z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP Business ByDesign**, vyberte **SAP Business ByDesign** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SAP Business ByDesign v seznamu výsledků](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP Business ByDesign podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SAP Business ByDesign je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SAP Business ByDesign musí navázat.

V SAP Business ByDesign, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP Business ByDesign, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  – Pokud chcete mít protějšek Britta Simon v ByDesign SAP Business, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SAP Business ByDesign.

**Ke konfiguraci Azure AD jednotné přihlašování s SAP Business ByDesign, proveďte následující kroky:**

1. Na portálu Azure na **SAP Business ByDesign** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Na **SAP Business ByDesign domény a adresy URL** část, proveďte následující kroky:

    ![SAP Business ByDesign domény a adresy URL jednotné přihlašování informace](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<servername>.sapbydesign.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) k získání těchto hodnot.

4. Na **uživatelské atributy** část, proveďte následující kroky:

    ![SAP Business ByDesign atribut části](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. V **uživatelský identifikátor** seznamu, vyberte **ExtractMailPrefix()** funkce.
    
    b. Z **e-mailu** vyberte atribut uživatele, kterou chcete použít týkající se vaší implementace. Například pokud chcete použít EmployeeID jako jedinečný identifikátor uživatele a hodnota atributu jsou uloženy v ExtensionAttribute2, pak vyberte user.extensionattribute2.     

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. Na **SAP Business ByDesign konfigurace** klikněte na tlačítko **konfigurace SAP Business ByDesign** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![SAP Business ByDesign konfigurace](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Pokud chcete získat jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, proveďte následující kroky:
   
    a. Přihlásit se k portálu SAP Business ByDesign s právy správce.
   
    b. Přejděte na **aplikace a běžné úlohy správy uživatele** a klikněte na tlačítko **zprostředkovatele Identity** kartě.
   
    c. Klikněte na tlačítko **nového poskytovatele Identity** a vyberte soubor XML metadat, kterou jste si stáhli z portálu Azure. Importováním metadata systém automaticky nahrává dodejka certifikát a certifikát pro šifrování.
   
    ![Konfigurovat jednotné přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Zahrnout **adresa URL služby příjemce Assertion** do žádost SAML, vyberte **zahrnují Assertion příjemce adresa URL služby**.
   
    e. Klikněte na tlačítko **aktivovat jednotné přihlašování**.
   
    f. Uložte provedené změny.
   
    g. Klikněte **Moje systému** kartě.
   
    ![Konfigurovat jednotné přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **Azure AD adresa URL přihlašování** textové pole.
   
    ![Konfigurovat jednotné přihlašování](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Zadejte, zda zaměstnanec ručně vybrat mezi přihlásit pomocí ID uživatele a heslo nebo jednotného přihlašování k výběrem **výběr zprostředkovatele Identity ruční**.
   
    j. V **jednotného přihlašování k adrese URL** části, zadejte adresu URL, který se má použít pro zaměstnance k přihlašování do systému. 
    V adresu URL odeslání zaměstnanec rozevíracího seznamu můžete zvolit z následujících možností:
   
    **Adresa URL jednotného přihlašování**
   
    Systém odešle jenom adresu URL normální systému zaměstnanci. Zaměstnanec nemůže přihlášení pomocí jednotného přihlašování a musí používat heslo nebo místo toho certifikátu.
   
    **ADRESA URL JEDNOTNÉHO PŘIHLAŠOVÁNÍ** 
   
    Systém odešle zaměstnanec pouze adresy URL pro jednotné přihlašování. Zaměstnanec může přihlásit pomocí jednotného přihlašování. Prostřednictvím rozšíření IdP přesměruje požadavek na ověření.
   
    **Automatický výběr**
   
    Pokud jednotného přihlašování není aktivní, odešle systému zaměstnanec adresu URL normální systému. Pokud je aktivní jednotné přihlašování, systém kontroluje, zda zaměstnanec má heslo. Pokud heslo je k dispozici, jednotného přihlašování k URL a adresy URL bez jednotného přihlašování k odešlou do jednotlivých zaměstnanců. Ale pokud zaměstnanec, nemá žádné heslo, pouze adresy URL pro jednotné přihlašování odešle zaměstnanci.
   
    k. Uložte provedené změny.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Vytvořit uživatele s SAP Business ByDesign testu

V této části vytvoříte uživatele volal Britta Simon v SAP Business ByDesign. Spojte se s [tým podpory SAP Business ByDesign klienta](https://www.sap.com/products/cloud-analytics.support.html) přidat uživatele do SAP Business ByDesign platformy. 

> [!NOTE]
> Ujistěte se, že NameID hodnotu shodovat s pole uživatelské jméno v platformě SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k SAP Business ByDesign Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SAP Business ByDesign, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP Business ByDesign**.

    ![V seznamu aplikací na SAP Business ByDesign odkaz](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SAP Business ByDesign na přístupovém panelu, jste měli získat automaticky přihlášení k SAP Business ByDesign aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png

