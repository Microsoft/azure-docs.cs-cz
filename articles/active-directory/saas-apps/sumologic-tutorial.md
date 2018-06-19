---
title: 'Kurz: Azure Active Directory integrace s SumoLogic | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 784509ea111b59521a3278813d45d76f191b8c1f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901025"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Kurz: Azure Active Directory integrace s SumoLogic

V tomto kurzu zjistěte, jak integrovat SumoLogic s Azure Active Directory (Azure AD).

Integrace SumoLogic s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SumoLogic
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SumoLogic (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SumoLogic, potřebujete následující položky:

- Předplatné služby Azure AD
- SumoLogic jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SumoLogic z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sumologic-from-the-gallery"></a>Přidání SumoLogic z Galerie
Při konfiguraci integrace SumoLogic do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS SumoLogic z galerie.

**Pokud chcete přidat SumoLogic z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SumoLogic**.

    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/tutorial_sumologic_search.png)

5. Na panelu výsledků vyberte **SumoLogic**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s SumoLogic podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SumoLogic je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SumoLogic musí navázat.

V SumoLogic, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SumoLogic, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SumoLogic](#creating-a-sumologic-test-user)**  – Pokud chcete mít protějšek Britta Simon v SumoLogic propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SumoLogic.

**Ke konfiguraci Azure AD jednotné přihlašování s SumoLogic, proveďte následující kroky:**

1. Na portálu Azure na **SumoLogic** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. Na **SumoLogic domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.SumoLogic.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory SumoLogic klienta](https://www.sumologic.com/contact-us/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_general_400.png)

6. Na **SumoLogic konfigurace** klikněte na tlačítko **konfigurace SumoLogic** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti SumoLogic jako správce.

8. Přejděte na **spravovat \> zabezpečení**.
   
    ![Spravovat](./media/sumologic-tutorial/ic778556.png "spravovat")

9. Klikněte na tlačítko **SAML**.
   
    ![Nastavení zabezpečení globální](./media/sumologic-tutorial/ic778557.png "nastavení globálního zabezpečení")

10. Z **vyberte konfiguraci, nebo vytvořte novou** vyberte **Azure AD**a potom klikněte na **konfigurace**.
   
    ![Konfigurace SAML 2.0](./media/sumologic-tutorial/ic778558.png "konfigurace SAML 2.0")

11. Na **konfigurace SAML 2.0** dialogové okno, proveďte následující kroky:
   
    ![Konfigurace SAML 2.0](./media/sumologic-tutorial/ic778559.png "konfigurace SAML 2.0")
   
    a. V **název konfigurace** textovému poli, typ **Azure AD**. 

    b. Vyberte **režim ladění**.

    c. V **vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure. 

    d. V **ověřovacího požadavku URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát X.509** textové pole.

    f. Jako **atribut e-mailu**, vyberte **pomocí SAML subjektu**.  

    g. Vyberte **SP iniciované konfigurace přihlášení**.

    h. V **přihlašovací cestu** textovému poli, typ **Azure** a klikněte na tlačítko **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sumologic-test-user"></a>Vytvoření zkušebního uživatele SumoLogic

Pokud chcete povolit uživatelům Azure AD přihlášení k SumoLogic, musí být zřízená k SumoLogic.  

* V případě SumoLogic zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **SumoLogic** klienta.

2. Přejděte na **spravovat \> uživatelé**.
   
    ![Uživatelé](./media/sumologic-tutorial/ic778561.png "uživatelů")

3. Klikněte na tlačítko **Add** (Přidat).
   
    ![Uživatelé](./media/sumologic-tutorial/ic778562.png "uživatelů")

4. Na **nového uživatele** dialogové okno, proveďte následující kroky:
   
    ![Nový uživatel](./media/sumologic-tutorial/ic778563.png "nového uživatele") 
 
    a. Zadejte související informace, které chcete zřídit do účtu Azure AD **křestní jméno**, **příjmení**, a **e-mailu** textových polí.
  
    b. Vyberte roli.
  
    c. Jako **stav**, vyberte **Active**.
  
    d. Klikněte na **Uložit**.

>[!NOTE]
>Můžete použít všechny ostatní SumoLogic uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované SumoLogic zřídit AAD uživatelské účty. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu SumoLogic.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon SumoLogic, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SumoLogic**.

    ![Konfigurovat jednotné přihlašování](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici SumoLogic na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci SumoLogic.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

