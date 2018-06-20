---
title: 'Kurz: Azure Active Directory integrace s ICIMS | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ICIMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72dbd649-e4b1-4d72-ad76-636d84922596
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 4a00815b682f2592799c46687ea429d8e24138e2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212287"
---
# <a name="tutorial-azure-active-directory-integration-with-icims"></a>Kurz: Azure Active Directory integrace s ICIMS

V tomto kurzu zjistěte, jak integrovat ICIMS s Azure Active Directory (Azure AD).

Integrace ICIMS s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ICIMS
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ICIMS (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ICIMS, potřebujete následující položky:

- Předplatné služby Azure AD
- ICIMS jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ICIMS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-icims-from-the-gallery"></a>Přidání ICIMS z Galerie
Při konfiguraci integrace ICIMS do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ICIMS z galerie.

**Pokud chcete přidat ICIMS z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ICIMS**, vyberte **ICIMS** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![ICIMS v seznamu výsledků](./media/icims-tutorial/tutorial_icims_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ICIMS podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ICIMS je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ICIMS musí navázat.

V ICIMS, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ICIMS, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s ICIMS](#create-an-icims-test-user)**  – Pokud chcete mít protějšek Britta Simon v ICIMS propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ICIMS.

**Ke konfiguraci Azure AD jednotné přihlašování s ICIMS, proveďte následující kroky:**

1. Na portálu Azure na **ICIMS** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/icims-tutorial/tutorial_icims_samlbase.png)

3. Na **ICIMS domény a adresy URL** část, proveďte následující kroky:

    ![ICIMS domény a adresy URL jednotné přihlašování informace](./media/icims-tutorial/tutorial_icims_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant name>.icims.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant name>.icims.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory ICIMS klienta](https://www.icims.com/contact-us) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/icims-tutorial/tutorial_icims_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/icims-tutorial/tutorial_general_400.png)

6. Na **ICIMS konfigurace** klikněte na tlačítko **konfigurace ICIMS** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace ICIMS](./media/icims-tutorial/tutorial_icims_configure.png) 

7. Konfigurace jednotného přihlašování na **ICIMS** straně, budete muset odeslat stažené **soubor XML s metadaty**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [ Tým podpory ICIMS](https://www.icims.com/contact-us). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/icims-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/icims-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/icims-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/icims-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-icims-test-user"></a>Vytvořit uživatele s ICIMS testu

Cílem této části je vytvoření uživatele v ICIMS nazývá Britta Simon. Práce s [tým podpory ICIMS](https://www.icims.com/contact-us) přidat uživatele do ICIMS účtu. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ICIMS.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon ICIMS, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ICIMS**.

    ![V seznamu aplikací na ICIMS odkaz](./media/icims-tutorial/tutorial_icims_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici ICIMS na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ICIMS.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/icims-tutorial/tutorial_general_01.png
[2]: ./media/icims-tutorial/tutorial_general_02.png
[3]: ./media/icims-tutorial/tutorial_general_03.png
[4]: ./media/icims-tutorial/tutorial_general_04.png

[100]: ./media/icims-tutorial/tutorial_general_100.png

[200]: ./media/icims-tutorial/tutorial_general_200.png
[201]: ./media/icims-tutorial/tutorial_general_201.png
[202]: ./media/icims-tutorial/tutorial_general_202.png
[203]: ./media/icims-tutorial/tutorial_general_203.png

