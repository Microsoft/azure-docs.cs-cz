---
title: 'Kurz: Azure Active Directory integrace s ADP Globalview | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 11a3df06cbd1c3f34bfd5b04c1f6dfc41cab8187
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223108"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Kurz: Azure Active Directory integrace s ADP Globalview

V tomto kurzu zjistěte, jak integrovat ADP Globalview s Azure Active Directory (Azure AD).

Integrace ADP Globalview s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ADP Globalview
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ADP Globalview (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ADP Globalview, potřebujete následující položky:

- Předplatné služby Azure AD
- ADP Globalview jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ADP Globalview z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adp-globalview-from-the-gallery"></a>Přidání ADP Globalview z Galerie
Při konfiguraci integrace ADP Globalview do služby Azure AD potřebujete přidat ADP Globalview z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat ADP Globalview z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ADP Globalview**.

    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. Na panelu výsledků vyberte **ADP Globalview**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testu Azure AD jednotné přihlašování s ADP Globalview podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ADP Globalview je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ADP Globalview musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v ADP Globalview.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ADP Globalview, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele ADP Globalview](#creating-an-adp-globalview-test-user)**  – Pokud chcete mít protějšek Britta Simon v ADP Globalview propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ADP Globalview.

**Ke konfiguraci Azure AD jednotné přihlašování s ADP Globalview, proveďte následující kroky:**

1. Na portálu Azure na **ADP Globalview** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. Na **ADP Globalview domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_url.png)

     V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.globalview.adp.com/federate` nebo `https://<subdomain>.globalview.adp.com/federate2`

    > [!NOTE] 
    > Hodnota není skutečné. Aktualizujte hodnotu skutečným identifikátorem. Obraťte se na [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) k získání hodnoty.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. Aplikace ADP GlobalView očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. 

6. Následující snímek obrazovky ukazuje příklad pro ni. Názvy deklarace identity se vždycky být **"PersonImmutableID"** a hodnoty, které jsme jsou namapovány ExtensionAttribute2, který obsahuje EmployeeID uživatele. Zde namapování uživatele z Azure AD na ADP GlobalView probíhá na EmployeeID ale můžete ji namapovat na jinou hodnotu také podle nastavení aplikace. Můžete spolupracovat s týmem ADP GlobalView nejprve k použijte správný identifikátor uživatele a mapování danou hodnotu s **"PersonImmutableID"** deklarací identity. Můžete také mapovat deklarace identity e-mailu a ID uživatele, jak je znázorněno na obrázku.

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | personalimmutableid | User.extensionattribute2 |
    | e-mail               | User.Mail |
    | ID uživatele              | user.userprincipalname|
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

    > [!NOTE] 
    > Před konfigurací kontrolního výrazu SAML, budete muset kontaktovat vaše [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) a požadovat hodnotu atributu jedinečný identifikátor pro vašeho klienta. Je nutné tuto hodnotu ke konfiguraci vlastních deklarací identity pro vaši aplikaci. 

8. Na **ADP Globalview konfigurace** klikněte na tlačítko **konfigurace ADP Globalview** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_general_400.png)

10. Konfigurace jednotného přihlašování na **ADP Globalview** straně, budete muset odeslat stažené **certifikátu (Base64)**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/create_aaduser_01.png) 

2.  Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/adglobalview-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-adp-globalview-test-user"></a>Vytvoření ADP Globalview testovacího uživatele

Cílem této části je vytvoření uživatele v ADP GlobalView nazývá Britta Simon. Práce s [ADP Globalview podporu](https://www.adp.com/contact-us/overview.aspx) přidat uživatele do ADP GlobalView účtu. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ADP Globalview.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon ADP Globalview, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ADP Globalview**.

    ![Konfigurovat jednotné přihlašování](./media/adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici ADP GlobalView na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci ADP GlobalView.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/adglobalview-tutorial/tutorial_general_203.png

