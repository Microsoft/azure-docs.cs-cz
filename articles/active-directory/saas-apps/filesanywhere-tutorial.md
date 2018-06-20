---
title: 'Kurz: Azure Active Directory integrace s FilesAnywhere | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FilesAnywhere.
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
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 8d80e537bc031a777ae037f5147b2c84b7e76281
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215966"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Kurz: Azure Active Directory integrace s FilesAnywhere

V tomto kurzu zjistěte, jak integrovat FilesAnywhere s Azure Active Directory (Azure AD).

Integrace FilesAnywhere s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FilesAnywhere
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FilesAnywhere (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FilesAnywhere, potřebujete následující položky:

- Předplatné služby Azure AD
- FilesAnywhere jednotného přihlašování povolené předplatné


> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.


Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FilesAnywhere z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-filesanywhere-from-the-gallery"></a>Přidání FilesAnywhere z Galerie
Při konfiguraci integrace FilesAnywhere do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS FilesAnywhere z galerie.

**Pokud chcete přidat FilesAnywhere z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **FilesAnywhere**.

    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

5. Na panelu výsledků vyberte **FilesAnywhere**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s FilesAnywhere podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v FilesAnywhere je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v FilesAnywhere musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v FilesAnywhere.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FilesAnywhere, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele FilesAnywhere](#creating-a-filesanywhere-test-user)**  – Pokud chcete mít protějšek Britta Simon v FilesAnywhere propojeném s Azure AD reprezentace jí.
3. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
4. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci FilesAnywhere.

**Ke konfiguraci Azure AD jednotné přihlašování s FilesAnywhere, proveďte následující kroky:**

1. Na portálu Azure Management portal na **FilesAnywhere** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

3. Na **FilesAnywhere domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**:

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Pamatujte, že hodnota **215** je **clientid** a je jenom jako příklad. Potřebujete nahradit hodnotou skutečné clientid.

4. Na **FilesAnywhere domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Klikněte na **zobrazit upřesňující nastavení adresy URL** možnost

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné přihlašovací adresa URL a adresa URL odpovědi. Obraťte se na [tým podpory FilesAnywhere](mailto:support@FilesAnywhere.com) k získání těchto hodnot. 

5. FilesAnywhere softwarová aplikace očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad pro tento.
    
    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Pokud se uživatelé přihlásí s FilesAnywhere se získat hodnotu **clientid** atribut z [FilesAnywhere team](mailto:support@FilesAnywhere.com). Budete muset přidat atribut "Id klienta" s poskytované FilesAnywhere jedinečných hodnot. Všechny tyto atributy, které jsou uvedené výše se vyžadují.
    > [!NOTE] 
    > Pamatujte, že hodnota **2331** z **clientid** je jenom jako příklad. Je třeba zadat skutečnou hodnotu.


6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | ClientID | *"uniquevalue"* |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **Ok**

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_general_400.png)

8. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

9. Na **FilesAnywhere konfigurace** klikněte na tlačítko **konfigurace FilesAnywhere** otevřete **konfigurovat přihlášení** okno.

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

10. Chcete-li získat dokončení konfigurace jednotného přihlašování pro vaši aplikaci na konci FilesAnywhere, obraťte se na [tým podpory FilesAnywhere](mailto:support@FilesAnywhere.com) a poskytněte stažené tokenu SAML podpisový certifikát a jednotné přihlašování na (SSO) adresy URL.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 



### <a name="creating-a-filesanywhere-test-user"></a>Vytvoření zkušebního uživatele FilesAnywhere

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele automaticky se vytvoří v aplikaci. 


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k FilesAnywhere Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon FilesAnywhere, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **FilesAnywhere**.

    ![Konfigurovat jednotné přihlašování](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    


### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici FilesAnywhere na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci FilesAnywhere.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
