---
title: 'Kurz: Azure Active Directory integrace s Marketo | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e7ff12938ea66bc218f71018247e70f0ebc1400d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226805"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Azure Active Directory integrace s Marketo

V tomto kurzu zjistěte, jak integrovat Marketo s Azure Active Directory (Azure AD).

Integrace služby Marketo s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke službě marketo získáte
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Marketo (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Marketo, potřebujete následující položky:

- Předplatné služby Azure AD
- Marketo jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Marketo z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-marketo-from-the-gallery"></a>Přidání Marketo z Galerie
Při konfiguraci integrace služby Marketo do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Marketo z galerie.

**Pokud chcete přidat Marketo z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Marketo**.

    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/tutorial_marketo_search.png)

5. Na panelu výsledků vyberte **Marketo**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Marketo podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Marketo je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Marketo musí navázat.

V Marketo, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Marketo, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Marketo](#creating-a-marketo-test-user)**  – Pokud chcete mít protějšek Britta Simon v Marketo propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Marketo.

**Ke konfiguraci Azure AD jednotné přihlašování s Marketo, proveďte následující kroky:**

1. Na portálu Azure na **Marketo** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

3. Na **Marketo domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://saml.marketo.com/sp`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory Marketo](http://investors.marketo.com/contactus.cfm) k získání těchto hodnot.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_general_400.png)

6. Na **Marketo konfigurace** klikněte na tlačítko **konfigurace Marketo** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_configure.png) 

7. Pokud chcete získat Id Munchkin vaší aplikace, přihlaste se ke službě marketo získáte pomocí přihlašovacích údajů správce a proveďte následující akce:
   
    a. Přihlaste se k aplikaci Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na **Munchkin odkaz**.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte Id Munchkin uvedené na obrazovce a dokončete vaše adresa URL odpovědi v Průvodci konfigurací služby Azure AD.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_12.png) 

8. Chcete-li nakonfigurovat jednotné přihlašování v aplikaci, postupujte následujících kroků:
   
    a. Přihlaste se k aplikaci Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na tlačítko **jednotné přihlašování**.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit SAML nastavení, klikněte na tlačítko **upravit** tlačítko.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povolit** nastavení jednotného přihlašování.
   
    f. Vložení **SAML Entity ID**v **ID vystavitele** textové pole.
   
    g. V **Entity ID** textovému poli, zadejte adresu URL jako `http://saml.marketo.com/sp`.
   
    h. Vyberte umístění ID uživatele jako **název identifikátor elementu**.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud není uživatelský identifikátor hodnota UPN a změnit hodnotu na kartě atribut.
   
    i. Nahrajte certifikát, který jste si stáhli z Průvodce konfigurací služby Azure AD. **Uložit** nastavení.
   
    j. Upravte nastavení přesměrování stránky.
   
    k. Vložení **SAML jeden přihlašování adresa URL služby** v **přihlašovací adresa URL** textové pole.
   
    l. Vložení **Sign-Out URL** v **adresy URL odhlašovací** textové pole.
   
    m. V **chyba URL**, kopie vaší **adresu URL instance Marketo** a klikněte na tlačítko **Uložit** tlačítko pro uložení nastavení.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_10.png)

9. Chcete-li povolit jednotné přihlašování pro uživatele, proveďte následující akce:
   
    a. Přihlaste se k aplikaci Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte na **zabezpečení** nabídky a klikněte na tlačítko **nastavení přihlášení**.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zkontrolujte **vyžadovat jednotné přihlašování** možnost a **Uložit** nastavení.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/marketo-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-marketo-test-user"></a>Vytvoření zkušebního uživatele Marketo

V této části vytvoříte uživatele volal Britta Simon v Marketo. postupujte podle těchto kroků můžete vytvořit uživateli v platformě Marketo.

1. Přihlaste se k aplikaci Marketo pomocí přihlašovacích údajů správce.

2. Klikněte **správce** tlačítko v horním navigačním podokně.
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Přejděte na **zabezpečení** nabídky a klikněte na tlačítko **uživatelé a role**
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klikněte **pozvat nového uživatele** odkaz na kartě Uživatelé
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. V Průvodci vytvořením nového uživatele pozvat, zadejte následující informace
   
    a. Zadejte uživatele **e-mailu** adresu do textového pole
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadejte **křestní jméno** do textového pole
   
    c. Zadejte **příjmení** do textového pole
   
    d. Klikněte na **Další**

6. V **oprávnění** vyberte **userRoles** a klikněte na tlačítko **další**
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klikněte **odeslat** tlačítko Poslat pozvánku uživatele
   
    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Uživatel obdrží e-mailových oznámení a klikněte na odkaz a změnit heslo, aby účet. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Marketo.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Marketo, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Marketo**.

    ![Konfigurovat jednotné přihlašování](./media/marketo-tutorial/tutorial_marketo_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Marketo na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Marketo.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

