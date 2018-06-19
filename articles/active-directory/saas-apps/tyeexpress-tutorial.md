---
title: 'Kurz: Azure Active Directory integrace s T & E Express | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 51366a440f6ce67d232ae8a3cb46674b26443981
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35897320"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Kurz: Azure Active Directory integrace s T & E Express

V tomto kurzu zjistěte, jak integrovat T & E Express s Azure Active Directory (Azure AD).

N & E Express integraci s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k & E Express
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k & E Express (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s T & E Express, potřebujete následující položky:

- Předplatné služby Azure AD
- T & E Express jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání T & E Express z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-te-express-from-the-gallery"></a>Přidání T & E Express z Galerie
Při konfiguraci integrace T & E Express do služby Azure AD, musíte přidat k & E Express z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat k & E Express z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **T & E Express**.

    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. Na panelu výsledků vyberte **T & E Express**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování s T & E Express podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v T & E Express je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v T & E Express je nutné stanovit.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v T & E Express.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s T & E Express, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele T & E Express](#creating-a-te-express-test-user)**  – Pokud chcete mít protějšek Britta Simon v T & E Express, která je propojený s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci T & E Express.

**Ke konfiguraci Azure AD jednotné přihlašování s T & E Express, proveďte následující kroky:**

1. Na portálu Azure Management portal na **T & E Express** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. Na **T & E Express domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu jako: `https://<domain>.tyeexpress.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečným identifikátorem a adresa URL odpovědi. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Obraťte se na [T & E Express tým podpory](http://www.tyeexpress.com/contacto.aspx) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tutorial_general_400.png)

8. Konfigurace jednotného přihlašování na **T & E Express** straně, přihlášení k T & E express aplikaci bez jednotného přihlašování SAML na pomocí přihlašovacích údajů správce.

9. V části **správce** klikněte na **SAML domény** otevřete stránku nastavení SAML.

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tye-SAML.png)

10. Vyberte **Activar(Activate)** možnost z **ne** k **SI(Yes)**. V **metadat zprostředkovatelů Identity** textovému poli, vložte metadata XML, které jste donwloaded z portálu Azure.

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tyeAdmin.png)

11. Klikněte na **Guardar(Save)** tlačítko pro uložení nastavení. 


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-te-express-test-user"></a>Vytvoření zkušebního uživatele T & E Express

Pokud chcete povolit uživatelům Azure AD přihlášení do n & E Express, musí být zřízená do n & E Express.  
V případě T & E Express zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti T & E Express jako správce.

2. V části značky správce klikněte na uživatelům otevřít uživatelé stránky předlohy.

    ![Můžete přidat zaměstnance](./media/tyeexpress-tutorial/tye-adminusers.png)

3. Na domovské stránce klikněte na **+** přidejte uživatele.

    ![Můžete přidat zaměstnance](./media/tyeexpress-tutorial/tye-usershome.png)

4. Zadejte povinné údaje, zobrazí výzva, ve formuláři a klikněte na tlačítko Uložit uložte podrobnosti.

    ![Můžete přidat zaměstnance](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Můžete přidat zaměstnance](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k & E Express.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k & E Express, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **T & E Express**.

    ![Konfigurovat jednotné přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici T & E Express na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci T & E Express.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

