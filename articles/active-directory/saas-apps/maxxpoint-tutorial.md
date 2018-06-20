---
title: 'Kurz: Azure Active Directory integrace s MaxxPoint | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MaxxPoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ba026e-96fc-4ae8-b135-0169da810e99
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 6be07aa807501072f232d4371c7a4ad4d3fac10b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225465"
---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Kurz: Azure Active Directory integrace s MaxxPoint

V tomto kurzu zjistěte, jak integrovat MaxxPoint s Azure Active Directory (Azure AD).

Integrace MaxxPoint s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k MaxxPoint
- Můžete povolit uživatelům, aby automaticky získat přihlášení k MaxxPoint (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s MaxxPoint, potřebujete následující položky:

- Předplatné služby Azure AD
- MaxxPoint jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání MaxxPoint z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování


## <a name="adding-maxxpoint-from-the-gallery"></a>Přidání MaxxPoint z Galerie
Při konfiguraci integrace MaxxPoint do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS MaxxPoint z galerie.

**Pokud chcete přidat MaxxPoint z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno pro přidání nové aplikace.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **MaxxPoint**.

    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_001.png)

5. Na panelu výsledků vyberte **MaxxPoint**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/tutorial_maxxpoint_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s MaxxPoint podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v MaxxPoint je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v MaxxPoint musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v MaxxPoint.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s MaxxPoint, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele MaxxPoint](#creating-a-maxxpoint-test-user)**  – Pokud chcete mít protějšek Britta Simon v MaxxPoint propojeném s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci MaxxPoint.

**Ke konfiguraci Azure AD jednotné přihlašování s MaxxPoint, proveďte následující kroky:**

1. Na portálu Azure na **MaxxPoint** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_general_300.png)

3. Na **MaxxPoint domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**, není nutné provádět žádné kroky.

    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
4. Na **MaxxPoint domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_03.png)

    a. Klikněte na tlačítko **zobrazit upřesňující nastavení adresy URL** možnost

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`

    > [!NOTE] 
    > Upozorňujeme, že se nejedná skutečné hodnoty. Budete muset aktualizovat tuto hodnotu s skutečné přihlašovací na adresy URL. Volání MaxxPoint týmu na **888-728-0950** získat tuto hodnotu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_general_400.png)

7. Jednotné přihlašování, které jsou nakonfigurované pro vaše aplikace získáte volání na tým podpory MaxxPoint **888-728-0950** a budete vám další pomůže o tom, jak poskytněte stažené **soubor XML s metadaty** souboru. 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na tlačítko **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/maxxpoint-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**. 

### <a name="creating-a-maxxpoint-test-user"></a>Vytvoření zkušebního uživatele MaxxPoint

V této části vytvoříte volal Britta Simon v MaxxPoint uživatele. Zavolejte tým podpory MaxxPoint na **888-728-0950** přidejte uživatele v aplikaci MaxxPoint.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k MaxxPoint Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon MaxxPoint, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **MaxxPoint**.

    ![Konfigurovat jednotné přihlašování](./media/maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici MaxxPoint na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci MaxxPoint.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/maxxpoint-tutorial/tutorial_general_203.png