---
title: 'Kurz: Azure Active Directory integrace s IMAGE FUNGUJE | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FUNGUJE bitové kopie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 635d86a1-b512-442d-8851-3b18ec1a24a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: ca99710f814a0fe48809fecb3d564e7360314a30
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222851"
---
# <a name="tutorial-azure-active-directory-integration-with-image-works"></a>Kurz: Azure Active Directory integrace s FUNGUJE bitové kopie

V tomto kurzu zjistěte, jak integrovat IMAGE FUNGUJE s Azure Active Directory (Azure AD).

Integrace IMAGE FUNGUJE s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k bitové kopie FUNGUJE.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FUNGUJE bitové kopie (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FUNGUJE bitové kopie, potřebujete následující položky:

- Předplatné služby Azure AD
- OBRÁZKU FUNGUJE jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání bitové kopie FUNGUJE z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-image-works-from-the-gallery"></a>Přidání bitové kopie FUNGUJE z Galerie
Při konfiguraci integrace FUNGUJE bitové kopie do služby Azure AD potřebujete přidat FUNGUJE bitové kopie z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat bitovou KOPII FUNGUJE z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **IMAGE FUNGUJE**, vyberte **IMAGE FUNGUJE** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![OBRÁZEK FUNGUJE v seznamu výsledků](./media/imageworks-tutorial/tutorial_imageworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí bitové kopie FUNGUJE podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v FUNGUJE bitové kopie je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v FUNGUJE bitové kopie je nutné stanovit.

V FUNGUJE bitové kopie, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FUNGUJE bitové kopie, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele IMAGE FUNGUJE](#create-a-image-works-test-user)**  – Pokud chcete mít protějšek Britta Simon v FUNGUJE bitové kopie, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci bitové kopie FUNGUJE.

**Ke konfiguraci Azure AD jednotné přihlašování s FUNGUJE bitové kopie, proveďte následující kroky:**

1. Na portálu Azure na **IMAGE FUNGUJE** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/imageworks-tutorial/tutorial_imageworks_samlbase.png)

3. Na **IMAGE FUNGUJE domény a adresy URL** část, proveďte následující kroky:

    ![OBRÁZEK FUNGUJE domény a adresy URL jednotné přihlašování informace](./media/imageworks-tutorial/tutorial_imageworks_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://i-imageworks.jp/iw/<tenantName>/sso/Login.do`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sp.i-imageworks.jp/iw/<tenantName>/postResponse`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory IMAGE FUNGUJE klient](mailto:iw-sd-support@fujifilm.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/imageworks-tutorial/tutorial_imageworks_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/imageworks-tutorial/tutorial_general_400.png)

6. Na **konfigurace FUNGUJE IMAGE** klikněte na tlačítko **konfigurace FUNGUJE IMAGE** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace FUNGUJE bitové kopie](./media/imageworks-tutorial/tutorial_imageworks_configure.png) 

7. Konfigurace jednotného přihlašování na **IMAGE FUNGUJE** straně, budete muset odeslat stažený **Certificate(Base64), Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování služby URL** k [FUNGUJE bitové kopie tým podpory](mailto:iw-sd-support@fujifilm.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/imageworks-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/imageworks-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/imageworks-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/imageworks-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-image-works-test-user"></a>Vytvoření zkušebního uživatele FUNGUJE bitové kopie

V této části vytvoříte uživatele volal Britta Simon v FUNGUJE bitové kopie. Práce s [tým podpory IMAGE FUNGUJE](mailto:iw-sd-support@fujifilm.com) přidat uživatele do FUNGUJE IMAGE platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu FUNGUJE bitové kopie.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon FUNGUJE bitové kopie, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **IMAGE FUNGUJE**.

    ![Odkaz OBRÁZKU FUNGUJE v seznamu aplikací](./media/imageworks-tutorial/tutorial_imageworks_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici FUNGUJE bitové kopie na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci bitové kopie FUNGUJE.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/imageworks-tutorial/tutorial_general_01.png
[2]: ./media/imageworks-tutorial/tutorial_general_02.png
[3]: ./media/imageworks-tutorial/tutorial_general_03.png
[4]: ./media/imageworks-tutorial/tutorial_general_04.png

[100]: ./media/imageworks-tutorial/tutorial_general_100.png

[200]: ./media/imageworks-tutorial/tutorial_general_200.png
[201]: ./media/imageworks-tutorial/tutorial_general_201.png
[202]: ./media/imageworks-tutorial/tutorial_general_202.png
[203]: ./media/imageworks-tutorial/tutorial_general_203.png

