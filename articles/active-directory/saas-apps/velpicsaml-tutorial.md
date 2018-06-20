---
title: 'Kurz: Azure Active Directory integrace s Velpic SAML | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: e7863d693a6c11e705990f9cfa2d1f54c75c7a91
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221304"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Kurz: Azure Active Directory integrace s Velpic SAML

V tomto kurzu zjistěte, jak integrovat Velpic SAML s Azure Active Directory (Azure AD).

Integrace Velpic SAML s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Velpic SAML
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Velpic SAML (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Velpic SAML, potřebujete následující položky:

- Předplatné služby Azure AD
- Velpic SAML jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Velpic SAML z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-velpic-saml-from-the-gallery"></a>Přidání Velpic SAML z Galerie
Při konfiguraci integrace Velpic SAML do služby Azure AD potřebujete přidat Velpic SAML z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Velpic SAML z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Velpic SAML**.

    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. Na panelu výsledků vyberte **Velpic SAML**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Velpic SAML podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Velpic SAML je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Velpic SAML musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Velpic SAML.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Velpic SAML, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Velpic SAML](#creating-a-velpic-saml-test-user)**  – Pokud chcete mít protějšek Britta Simon v Velpic SAML, propojené služby Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci Velpic SAML.

**Ke konfiguraci Azure AD jednotné přihlašování s Velpic SAML, proveďte následující kroky:**

1. Na portálu Azure Management portal na **Velpic SAML** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Zadejte podrobnosti v **domény Velpic SAML a adres URL** části -

    ![Konfigurovat jednotné přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte hodnotu jako: `https://<sub-domain>.velpicsaml.net`

    b. V **identifikátor** textovému poli, Vložit **'jednotné přihlašování na adresu URL,** hodnota `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Upozorňujeme, že adresa URL přihlašování budou poskytovat týmem Velpic SAML a hodnotu identifikátoru bude k dispozici, když konfigurujete jednotného přihlašování k modulu plug-in na straně Velpic SAML. Budete muset zkopírujte tuto hodnotu ze stránky aplikace Velpic SAML a vložte ho sem.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/velpicsaml-tutorial/tutorial_general_400.png)

6. V části Konfigurace SAML Velpic klikněte na tlačítko Konfigurovat Velpic SAML otevřete konfigurovat přihlašování okno. Zkopírujte SAML Entity ID z části Stručná referenční příručka.

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Velpic SAML.

8. Klikněte na **spravovat** kartě a přejděte na **integrace** část, kde je potřeba klikněte na **modulů plug-in** tlačítko pro vytvoření nového modulu plug-in pro přihlášení.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_1.png)

9. Klikněte na **'Přidání modulu plug-in'** tlačítko.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_2.png)

10. Klikněte na **SAML** dlaždici na stránce Přidat modul plug-in.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_3.png)

11. Zadejte název nové zásuvný modul SAML a klikněte na **"Přidat"** tlačítko.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_4.png)

12. Zadejte podrobnosti následujícím způsobem:

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. V **název** textovému poli, zadejte název zásuvný modul SAML.

    b. V **URL vystavitele** textovému poli, Vložit **SAML Entity ID** jste zkopírovali z **konfigurovat přihlášení** okno portálu Azure.

    c. V **zprostředkovatele metadat konfigurace** nahrát soubor XML s metadaty soubor, který jste si stáhli z portálu Azure.

    d. Můžete také povolit SAML jenom při zřizování čas povolením **, automaticky vytvoří nové uživatele'** zaškrtávací políčko. Pokud uživatel neexistuje v Velpic a tento příznak není povolen, přihlašovací údaje z Azure se nezdaří. Pokud je povoleno příznak uživatele se automaticky zřídí do Velpic při přihlášení. 

    e. Kopírování **jednotné přihlašování na adrese URL** z textu pole a vložte ji na portálu Azure.
    
    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Vytvoření zkušebního uživatele Velpic SAML

Tento krok se obvykle nevyžaduje jako aplikace podporuje jenom při zřizování uživatelů čas. Pokud uživatel automatické zřizování není povolena můžete vytvoření ruční uživatele provádí, jak je popsáno níže.

Přihlaste se k serveru vaší společnosti Velpic SAML jako správce a proveďte následující kroky:
    
1. Klikněte na kartu Správa a přejděte k části uživatelů a potom klikněte na tlačítko Nový přidat uživatele.

    ![Přidat uživatele](./media/velpicsaml-tutorial/velpic_7.png)

2. Na **"Vytvořit nový uživatel"** dialogové okno proveďte následující kroky.

    ![uživatel](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. V **křestní jméno** textovému poli, název typu první Britta Simon.

    b. V **příjmení** textovému poli, zadejte příjmení Britta Simon.

    c. V **uživatelské jméno** textovému poli, zadejte uživatelské jméno Britta Simon.

    d. V **e-mailu** textovému poli, zadejte e-mailovou adresu účtu Britta Simon.

    e. Zbývající informace je volitelné, můžete vyplnit, v případě potřeby.
    
    f. Klikněte na **ULOŽIT**.  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k Velpic SAML.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Velpic SAML, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Velpic SAML**.

    ![Konfigurovat jednotné přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

1. Když kliknete na dlaždici Velpic SAML na přístupovém panelu, měli byste obdržet přihlašovací stránku aplikace Velpic SAML. Měli byste vidět **"přihlásit se přes Azure AD,** tlačítko na přihlašovací stránce.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_6.png)

2. Klikněte na **"přihlásit se přes Azure AD,** tlačítko pro přihlášení do Velpic pomocí účtu Azure AD.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

