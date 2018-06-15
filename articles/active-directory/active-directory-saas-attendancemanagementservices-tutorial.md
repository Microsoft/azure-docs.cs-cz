---
title: 'Kurz: Azure Active Directory integraci se službou správy návštěvnosti | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi návštěvnosti služby správy a Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: abf882d7b06083080ad5cb3c2a20390a76a48139
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338886"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Kurz: Azure Active Directory integraci se službou správy návštěvnosti

V tomto kurzu zjistěte, jak integrovat návštěvnosti Management Services s Azure Active Directory (Azure AD).

Integrace služby pro návštěvnosti s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup ke službám návštěvnosti správy.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k účasti Management Services (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Při konfiguraci Azure AD integrace se službami pro správu návštěvnosti, potřebujete následující položky:

- Předplatné služby Azure AD
- Návštěvnosti služby pro jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání služby pro návštěvnosti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-attendance-management-services-from-the-gallery"></a>Přidání služby pro návštěvnosti z Galerie
Při konfiguraci integrace služby pro návštěvnosti do služby Azure AD, potřebujete přidat služby správy návštěvnosti z Galerie si na seznam spravovaných aplikací SaaS.

**Přidání služby pro návštěvnosti z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **návštěvnosti Management Services**, vyberte **návštěvnosti Management Services** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Služby pro účast v seznamu výsledků](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí služby správy návštěvnosti podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšek v návštěvnosti Management Services je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v návštěvnosti Management Services je nutné stanovit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se službami pro správu návštěvnosti, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s návštěvnosti Management Services](#create-an-attendance-management-service-test-user)**  – Pokud chcete mít protějšek Britta Simon ve návštěvnosti Management Services, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci návštěvnosti Management Services.

**Ke konfiguraci Azure AD jednotné přihlašování se službami pro správu návštěvnosti, proveďte následující kroky:**

1. Na portálu Azure na **návštěvnosti Management Services** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. Na **návštěvnosti doména služby správy a adresy URL** část, proveďte následující kroky:

    ![Doména služby správy návštěvnosti a adresy URL jednotné přihlašování informace](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://id.obc.jp/<tenant information >/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory návštěvnosti Management Services Client](http://www.obcnet.jp/) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_400.png)

6. Na **konfigurace služby správy návštěvnosti** klikněte na tlačítko **konfigurace služby pro návštěvnosti** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace služby správy návštěvnosti](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. V okně jiný prohlížeč přihlašování k webu společnosti návštěvnosti Management Services jako správce.

8. Klikněte na **ověřování SAML** pod **oddílu management zabezpečení**.

    ![Konfigurace služby správy návštěvnosti](./media/active-directory-saas-attendancemanagementservices-tutorial/user1.png)

9. Proveďte následující kroky:

    ![Konfigurace služby správy návštěvnosti](./media/active-directory-saas-attendancemanagementservices-tutorial/user2.png)

    a. Vyberte **ověřování pomocí SAML**.

    b. V **identifikátor** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure. 

    c. V **adresu URL koncového bodu ověřování** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    d. Klikněte na tlačítko **vyberte soubor** na kterou odešlete certifikát, který jste si stáhli z Azure AD.

    e. Vyberte **zakažte ověřování hesla**.

    f. Klikněte na tlačítko **registrace**

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace! Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Vytvořit testovací uživatele s návštěvnosti Management Services

Pokud chcete povolit uživatelům Azure AD přihlášení do služby pro návštěvnosti, musí být zřízená do návštěvnosti Management Services. V případě návštěvnosti Management Services zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti návštěvnosti Management Services jako správce.

2. Klikněte na **Správa uživatelů** pod **oddílu management zabezpečení**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-attendancemanagementservices-tutorial/user5.png)

3. Klikněte na tlačítko **nové pravidel přihlášení**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-attendancemanagementservices-tutorial/user3.png)

4. V **OBCiD informace** část, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/active-directory-saas-attendancemanagementservices-tutorial/user4.png)

    a. V **OBCiD** jako typ e-mailu uživatele k textovému poli, **BrittaSimon@contoso.com**.

    b. V **heslo** textovému poli, zadejte heslo uživatele.

    c. Klikněte na tlačítko **registrace**


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí služby správy návštěvnosti udělení přístupu.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon návštěvnosti Management Services, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **návštěvnosti Management Services**.

    ![Propojení služby pro účast v seznamu aplikací](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici služby pro účast na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci návštěvnosti Management Services.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_203.png

