---
title: 'Kurz: Azure Active Directory integrace s FirmPlay - hájící zájmy zaměstnanec pro nábor | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FirmPlay - hájící zájmy zaměstnanec pro nábor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: b154901d2e31f493c32e47bd331cc2d4e9fdc1a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342276"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Kurz: Azure Active Directory integrace s FirmPlay - hájící zájmy zaměstnanec pro nábor

V tomto kurzu zjistěte, jak integrovat FirmPlay - hájící zájmy zaměstnanec pro nábor službou Azure Active Directory (Azure AD).

Integrace FirmPlay - hájící zájmy zaměstnanec pro nábor s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FirmPlay - hájící zájmy zaměstnanec pro nábor
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FirmPlay - hájící zájmy zaměstnanec pro nábor (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FirmPlay - hájící zájmy zaměstnanec pro nábor, potřebujete následující položky:

- Předplatné služby Azure AD
- FirmPlay - hájící zájmy zaměstnanec pro jednotné přihlašování v předplatném povolené o přijetí


> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.


Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FirmPlay - hájící zájmy zaměstnanec pro nábor z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Přidání FirmPlay - hájící zájmy zaměstnanec pro nábor z Galerie
Konfigurace integrace FirmPlay - hájící zájmy zaměstnanec pro nábor do služby Azure AD, potřebujete přidat FirmPlay - hájící zájmy zaměstnanec pro nábor z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat FirmPlay - hájící zájmy zaměstnanec pro nábor z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **FirmPlay - hájící zájmy zaměstnanec pro nábor**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. Na panelu výsledků vyberte **FirmPlay - hájící zájmy zaměstnanec pro nábor**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování s FirmPlay - hájící zájmy zaměstnanec pro nábor podle testovacího uživatele názvem "Britta Simon".

Pro jednotné přihlašování pro práci Azure AD je potřeba vědět, jaké příslušného uživatele v FirmPlay – hájící zájmy zaměstnanec pro nábor je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v FirmPlay - hájící zájmy zaměstnanec pro přijímání musí být vytvořeno.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v FirmPlay - hájící zájmy zaměstnanec pro nábor.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FirmPlay - hájící zájmy zaměstnanec pro nábor, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření FirmPlay - hájící zájmy zaměstnanec pro nábor testovacího uživatele](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  – Pokud chcete mít protějšek Britta Simon v FirmPlay: hájící zájmy zaměstnanců, pro který je o přijetí propojené s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v vaší FirmPlay - hájící zájmy zaměstnanec nábor aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s FirmPlay - hájící zájmy zaměstnanec pro nábor, proveďte následující kroky:**

1. Na portálu Azure Management portal na **FirmPlay - hájící zájmy zaměstnanec pro nábor** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Na **FirmPlay - hájící zájmy zaměstnanec domény o přijetí a adresy URL** v části **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Upozorňujeme, že se nejedná skutečné hodnoty. Budete muset aktualizovat tuto hodnotu s skutečné přihlašovací na adresy URL. Obraťte se na [FirmPlay - hájící zájmy zaměstnanec pro tým podpory nábor](mailto:engineering@firmplay.com) získat tuto hodnotu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. Na **vytvořit nový certifikát** dialogové okno, klikněte na ikonu kalendáři a vyberte **datum vypršení platnosti**. Pak klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Na **SAML podpisový certifikát** vyberte **aktivujte nový certifikát** a klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. V místní nabídce **certifikát výměny** okně klikněte na tlačítko **OK**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (base64)** a potom uložte soubor certifikátu v počítači. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. Na **FirmPlay - hájící zájmy zaměstnanec pro přijetí konfigurace** klikněte na tlačítko **konfigurace FirmPlay - hájící zájmy zaměstnanec pro nábor** otevřete **konfigurovat přihlášení** Dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Chcete-li získat jednotné přihlašování, které jsou nakonfigurované pro vaše aplikace, obraťte se na [FirmPlay - hájící zájmy zaměstnanec pro tým podpory nábor](mailto:engineering@firmplay.com) a poskytněte jim následující: 

    • Stažené **soubor certifikátu**

    • **Adresa URL služby jednotného přihlašování SAML**

    • **SAML Entity ID**

    • **Odhlášení adresy URL**
  

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Vytváření FirmPlay - hájící zájmy zaměstnanec pro nábor testovacího uživatele

V této části vytvoříte volal Britta Simon v FirmPlay - hájící zájmy zaměstnanec pro nábor uživatele. Spojte se s [FirmPlay - hájící zájmy zaměstnanec pro tým podpory nábor](mailto:engineering@firmplay.com) přidat uživatele do FirmPlay - hájící zájmy zaměstnanec pro nábor platformu.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k FirmPlay - hájící zájmy zaměstnanec pro nábor Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit FirmPlay - hájící zájmy zaměstnanec pro nábor, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **FirmPlay - hájící zájmy zaměstnanec pro nábor**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    


### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko FirmPlay - hájící zájmy zaměstnanec pro dlaždici nábor na přístupovém panelu jste měli získat automaticky přihlášení k vaší FirmPlay - hájící zájmy zaměstnanec nábor aplikace.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png