---
title: 'Kurz: Azure Active Directory integrace s RedBrick stavu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RedBrick stavu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: c30da2ecebd7c46e8396351f7e7c5ee69c130e29
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217030"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Kurz: Azure Active Directory integrace s RedBrick stavu

V tomto kurzu zjistěte, jak integrovat RedBrick stavu s Azure Active Directory (Azure AD).

Integrace RedBrick stavu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k RedBrick stavu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k RedBrick stavu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RedBrick stavu, potřebujete následující položky:

- Předplatné služby Azure AD
- RedBrick stavu jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RedBrick stavu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-redbrick-health-from-the-gallery"></a>Přidání RedBrick stavu z Galerie
Při konfiguraci integrace RedBrick stavu do služby Azure AD potřebujete přidat RedBrick stavu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat RedBrick stavu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **RedBrick stavu**, vyberte **RedBrick stavu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![RedBrick stavu v seznamu výsledků](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s RedBrick stavu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v RedBrick stavu je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v RedBrick stavu.

V RedBrick stavu přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RedBrick stavu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele RedBrick stavu](#create-a-redbrick-health-test-user)**  – Pokud chcete mít protějšek Britta Simon v RedBrick stav, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci RedBrick stavu.

**Ke konfiguraci Azure AD jednotné přihlašování s RedBrick stavu, proveďte následující kroky:**

1. Na portálu Azure na **RedBrick stavu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. Na **RedBrick stavu domény a adresy URL** část, proveďte následující kroky:

    ![RedBrick stavu domény a adresy URL jednotné přihlašování informace](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `http://www.redbrickhealth.com`
    
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Pro produkční prostředí: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Klikněte na tlačítko **zobrazit upřesňující nastavení adresy URL**.
    
    ![RedBrick stavu domény a adresy URL jednotné přihlašování informace](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. V **předávání stavu** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Hodnota stavu předávání není skutečné. Aktualizujte tato hodnota se skutečným stavem předávání. Obraťte se na [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) získat tuto hodnotu.

4. Aplikace RedBrick stavu očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Tyto deklarace jsou specifické pro odběratele a závisí na váš požadavek. Následující volitelné deklarace identity jsou příklad pouze které můžete nakonfigurovat pro vaši aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** části na stránce integrace aplikace.

    ![Konfigurovat jednotné přihlašování](./media/redbrickhealth-tutorial/attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | hlavní název | ********** |
    | ID klienta | ********** |
    | účastnické id | ********** |
    
    > [!NOTE]
    > Tyto hodnoty jsou pro pouze referenční účely. Je třeba definovat atributy podle požadavků vaší organizace. Obraťte se na [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) získat další informace o požadované deklarace identity.
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.
    
    ![Konfigurovat jednotné přihlašování](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

6. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/redbrickhealth-tutorial/tutorial_general_400.png)

8. Na **RedBrick konfigurace stavu** klikněte na tlačítko **konfigurace RedBrick stavu** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace redBrick stavu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Konfigurace jednotného přihlašování na **RedBrick stavu** straně, budete muset odeslat stažené **Certificate(Base64)** a **SAML Entity ID** k [RedBrick stavu tým podpory](https://home.redbrickhealth.com/contact/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/redbrickhealth-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/redbrickhealth-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/redbrickhealth-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-redbrick-health-test-user"></a>Vytvoření zkušebního uživatele RedBrick stavu

V této části vytvoříte volal Britta Simon v RedBrick stavu uživatele. Práce s [tým podpory RedBrick stavu](https://home.redbrickhealth.com/contact/) přidat uživatele do platformy RedBrick stavu. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu RedBrick stavu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon RedBrick stavu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **RedBrick stavu**.

    ![V seznamu aplikací na odkaz RedBrick stavu](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici RedBrick stavu na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci RedBrick stavu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

