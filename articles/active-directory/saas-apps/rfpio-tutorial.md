---
title: 'Kurz: Azure Active Directory integrace s RFPIO | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: eda20d60d04ba8e50c31697b2f8775f2539088f3
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903371"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Kurz: Azure Active Directory integrace s RFPIO

V tomto kurzu zjistěte, jak integrovat RFPIO s Azure Active Directory (Azure AD).

Integrace RFPIO s Azure AD poskytuje následující výhody:

- Můžete určovat, kdo ve službě Azure AD, který má přístup k RFPIO.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k RFPIO (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RFPIO, potřebujete následující položky:

- Předplatné služby Azure AD.
- RFPIO jednotného přihlašování na povolené předplatné.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Pokud to není nezbytné, nepoužívejte produkční prostředí.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat [zkušební verze jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RFPIO z galerie.
2. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-rfpio-from-the-gallery"></a>Přidat RFPIO z Galerie
Při konfiguraci integrace RFPIO do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS RFPIO z galerie.

### <a name="to-add-rfpio-from-the-gallery"></a>Chcete-li přidat RFPIO z Galerie

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním podokně, vyberte **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Vyberte **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **RFPIO**.

    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/tutorial_rfpio_search.png)

5. Na panelu výsledků vyberte **RFPIO**a pak vyberte **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s RFPIO podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co je vztah mezi uživatelem protějškem v RFPIO a uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v RFPIO musí navázat.

V RFPIO, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RFPIO, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**– Chcete-li povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#creating-an-azure-ad-test-user)**– Azure AD jednotné přihlašování s Britta Simon otestovat.
3. **[Vytvoření zkušebního uživatele RFPIO](#creating-a-rfpio-test-user)**  – tak, aby měl protějšek Britta Simon v RFPIO propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**– Chcete-li povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#testing-single-sign-on)**  – Chcete-li ověřit, pokud konfigurace fungovat.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci RFPIO.

**Ke konfiguraci Azure AD jednotné přihlašování s RFPIO, proveďte následující kroky:**

1. Na portálu Azure na **RFPIO** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Na **RFPIO domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://www.rfpio.com`

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**.

    c. V **předávání stavu** textového pole zadejte hodnotu řetězce. Obraťte se na [tým podpory RFPIO](https://www.rfpio.com/contact/) získat tuto hodnotu. 

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu: 

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    V **přihlásit na adrese URL** textovému poli, zadejte adresu URL: `https://www.app.rfpio.com`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_general_400.png)

7. V okně prohlížeče jiný web, přihlášení, které **RFPIO** webu jako správce.

8. Klikněte na rozevírací nabídce dolního rohu.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app1.png)

9. Klikněte na **nastavení organizace**. 

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app2.png)

10. Klikněte na **funkce a integrace**.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app4.png)

11. V **Konfigurace jednotného přihlašování SAML** klikněte na tlačítko **upravit**.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app3.png)

12. V této části proveďte následující akce:

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app5.png)
    
    a. Zkopírujte obsah **stáhnout soubor XML s metadaty** a vložte ji do **konfigurace identity** pole.

    > [!NOTE]
    >Zkopírujte obsah stáhnout **soubor XML s metadaty** použití **Poznámkový blok ++** nebo správné **editoru XML**. 

    b. Klikněte na tlačítko **ověření**.

    c. Po kliknutí na **ověření**, podle **SAML(Enabled)** na on.

    d. Klikněte na tlačítko **odeslání**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rfpio-test-user"></a>Vytvoření zkušebního uživatele RFPIO

Pokud chcete povolit uživatelům Azure AD přihlášení k RFPIO, musí být zřízená do RFPIO.  
V případě RFPIO zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti RFPIO jako správce.

2. Klikněte na rozevírací nabídce dolního rohu.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app1.png)

3. Klikněte na **nastavení organizace**. 

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app2.png)

4. Klikněte na tlačítko **členové týmu**.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app6.png)

5. Klikněte na **přidat členy**.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app7.png)

6. V **přidat nové členy** části. Proveďte následující akce:

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/app8.png)

    a. Zadejte **e-mailová adresa** v **zadejte jednu e-mailovou na každý řádek** pole.

    b. Vyberte Plese **Role** podle požadavků.

    c. Klikněte na tlačítko **přidat členy**.
        
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu RFPIO.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon RFPIO, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **RFPIO**.

    ![Konfigurovat jednotné přihlašování](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když kliknete na dlaždici RFPIO na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci RFPIO.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o tom, jak integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

