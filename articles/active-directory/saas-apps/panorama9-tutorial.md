---
title: 'Kurz: Azure Active Directory integrace s Panorama9 | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: a6b3751361b198126c2d05d1650acbf89d2bbc1b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215796"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Kurz: Azure Active Directory integrace s Panorama9

V tomto kurzu zjistěte, jak integrovat Panorama9 s Azure Active Directory (Azure AD).

Integrace Panorama9 s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Panorama9
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Panorama9 (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Panorama9, potřebujete následující položky:

- Předplatné služby Azure AD
- Panorama9 jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Panorama9 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-panorama9-from-the-gallery"></a>Přidání Panorama9 z Galerie
Při konfiguraci integrace Panorama9 do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Panorama9 z galerie.

**Pokud chcete přidat Panorama9 z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Panorama9**.

    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/tutorial_panorama9_search.png)

5. Na panelu výsledků vyberte **Panorama9**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panorama9 podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Panorama9 je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Panorama9 musí navázat.

V Panorama9, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Panorama9, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Panorama9](#creating-a-panorama9-test-user)**  – Pokud chcete mít protějšek Britta Simon v Panorama9 propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Panorama9.

**Ke konfiguraci Azure AD jednotné přihlašování s Panorama9, proveďte následující kroky:**

1. Na portálu Azure na **Panorama9** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. Na **Panorama9 domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://dashboard.panorama9.com/saml/access/3262`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Panorama9 klienta](https://support.panorama9.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_general_400.png)

6. Na **Panorama9 konfigurace** klikněte na tlačítko **konfigurace Panorama9** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

5. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Panorama9.

6. Na panelu nástrojů v horní části klikněte na tlačítko **spravovat**a potom klikněte na **rozšíření**.
   
   ![Rozšíření](./media/panorama9-tutorial/ic790023.png "rozšíření")
7. Na **rozšíření** dialogové okno, klikněte na tlačítko **jednotné přihlašování**.
   
   ![Jednotné přihlašování](./media/panorama9-tutorial/ic790024.png "jednotného přihlašování")
8. V **nastavení** část, proveďte následující kroky:
   
   ![Nastavení](./media/panorama9-tutorial/ic790025.png "nastavení")
   
    a. V **adresa URL poskytovatele Identity** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
   
    b. V **otisků prstů certifikátů** textovému poli, Vložit **kryptografický otisk** hodnota certifikát, který jste zkopírovali z portálu Azure.    
         
9. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-panorama9-test-user"></a>Vytvoření zkušebního uživatele Panorama9

Pokud chcete povolit uživatelům Azure AD přihlášení do Panorama9, musí být zřízená do Panorama9.  

V případě Panorama9 zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Panorama9** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **spravovat**a potom klikněte na **uživatelé**.
   
  ![Uživatelé](./media/panorama9-tutorial/ic790027.png "uživatelů")

3. V sekci uživatelé klikněte na tlačítko **+** přidat nového uživatele.

 ![Uživatelé](./media/panorama9-tutorial/ic790028.png "uživatelů")

4. Přejděte k části dat uživatele, zadejte e-mailovou adresu chcete zřídit do platného uživatele Azure Active Directory **e-mailu** textové pole.

5. Se do části uživatelé klikněte na tlačítko **Uložit**.
   
> [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a dodržuje odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Panorama9.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Panorama9, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Panorama9**.

    ![Konfigurovat jednotné přihlašování](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Panorama9 na přístupovém panelu, jste měli získat automaticky přihlášení k Panorama9 aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

