---
title: 'Kurz: Azure Active Directory integrace s Learningpool Act | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Learningpool akce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 278cd755898fa082ab8ae825a7cb6be300548eb4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226239"
---
# <a name="tutorial-azure-active-directory-integration-with-learningpool-act"></a>Kurz: Azure Active Directory integrace s Learningpool Act

V tomto kurzu zjistěte, jak integrovat Learningpool Act s Azure Active Directory (Azure AD).

Integrace s Azure AD Application Compatibility Toolkit Learningpool poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Learningpool Act
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Learningpool Act (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Learningpool Application Compatibility Toolkit, potřebujete následující položky:

- Předplatné služby Azure AD
- Learningpool Act jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Application Compatibility Toolkit Learningpool z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-learningpool-act-from-the-gallery"></a>Přidání Application Compatibility Toolkit Learningpool z Galerie
Při konfiguraci integrace Learningpool Act do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Learningpool Act z galerie.

**Pokud chcete přidat Learningpool Act z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Learningpool Act**.

    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/tutorial_Learningpoolact_search.png)

5. Na panelu výsledků vyberte **Learningpool Act**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/tutorial_Learningpoolact_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Learningpool Act podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Learningpool Act je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Learningpool Act musí navázat.

V Learningpool Act přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Learningpool akce, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Learningpool Act](#creating-a-learningpool-act-test-user)**  – Pokud chcete mít protějšek Britta Simon v Learningpool Application Compatibility Toolkit, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Learningpool akce.

**Ke konfiguraci Azure AD jednotné přihlašování s Learningpool Application Compatibility Toolkit, proveďte následující kroky:**

1. Na portálu Azure na **Learningpool Act** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_samlbase.png)

3. Na **Learningpool Act domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<subdomain>.Learningpool.com/shibboleth` |
    | `https://<subdomain>.preview.Learningpool.com/shibboleth` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Learningpool Act klienta](https://www.Learningpool.com/support) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_certificate.png) 

5. Aplikace Learningpool Act očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **"Atrribute"** aplikace. Následující snímek obrazovky ukazuje příklad pro tento. 

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_attribute.png) 

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | název urn: oid:1.2.840.113556.1.4.221 | user.userprincipalname |
    | název urn: oid:2.5.4.42 | user.givenname |
    | urn:oid:0.9.2342.19200300.100.1.3 | User.Mail |    
    | název urn: oid:2.5.4.4 | user.surname |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Ponechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_general_400.png)

8. Konfigurace jednotného přihlašování na **Learningpool Act** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Learningpool Act](https://www.Learningpool.com/support). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/learningpool-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-learningpool-act-test-user"></a>Vytvoření zkušebního uživatele Learningpool Act

Povolit uživatelům Azure AD přihlášení k Learningpool Application Compatibility Toolkit, musí být zřízená do Learningpool akce.

Neexistuje žádná položka akce můžete nakonfigurovat zřizování na základě možnosti jednat Learningpool uživatelů.  
Uživatelé musí být vytvořený vaše [tým podpory Learningpool Act](https://www.Learningpool.com/support).

>[!NOTE]
>Můžete použít všechny ostatní Learningpool Act uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Learningpool Act zřídit AAD uživatelské účty. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Learningpool akce.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Learningpool Application Compatibility Toolkit, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Learningpool Act**.

    ![Konfigurovat jednotné přihlašování](./media/learningpool-tutorial/tutorial_Learningpoolact_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Learningpool Act na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Learningpool akce.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/Learningpool-tutorial/tutorial_general_01.png
[2]: ./media/Learningpool-tutorial/tutorial_general_02.png
[3]: ./media/Learningpool-tutorial/tutorial_general_03.png
[4]: ./media/Learningpool-tutorial/tutorial_general_04.png

[100]: ./media/Learningpool-tutorial/tutorial_general_100.png

[200]: ./media/Learningpool-tutorial/tutorial_general_200.png
[201]: ./media/Learningpool-tutorial/tutorial_general_201.png
[202]: ./media/Learningpool-tutorial/tutorial_general_202.png
[203]: ./media/Learningpool-tutorial/tutorial_general_203.png

