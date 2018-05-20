---
title: 'Kurz: Azure Active Directory integrace s Zscaler privátní přístup správce | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscaler privátní přístup správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: c3918e4b54d60d6f609e99e24ed2f4b2995bf80c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Kurz: Azure Active Directory integrace s Zscaler privátní přístup správce

V tomto kurzu zjistěte, jak integrovat Zscaler privátní přístup správce s Azure Active Directory (Azure AD).

Integrace Zscaler privátní přístup správce s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Zscaler privátní přístup správce.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k Zscaler privátní přístup správce (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Zscaler privátní přístup správce, potřebujete následující položky:

- Předplatné služby Azure AD
- Zscaler privátní přístup správce jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání správce přístupu Zscaler privátní z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Přidání správce přístupu Zscaler privátní z Galerie
Při konfiguraci integrace služby Správce přístupu Zscaler privátní do služby Azure AD, potřebujete přidat správce přístupu Zscaler privátní z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat správce přístupu Zscaler privátní z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zscaler privátní přístup správce**, vyberte **Zscaler privátní přístup správce** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![Správce přístupu Zscaler privátní v seznamu výsledků](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler privátní správce přístupu na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v modulu snap-in Správce přístupu privátní Zscaler je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Zscaler privátní přístup správce.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscaler privátní přístup správce, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele správce přístupu privátní Zscaler](#create-a-zscaler-private-access-administrator-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zscaler privátní přístup správce, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zscaler privátní přístup správce.

**Pokud chcete konfigurovat Azure AD jednotné přihlašování s Zscaler privátní přístup správce, proveďte následující kroky:**

1. Na portálu Azure na **Zscaler privátní přístup správce** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Na **Zscaler privátní přístup správce domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Zscaler privátní přístup správce domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**

    d. V **RelayState** textovému poli, typ a hodnotu: `idpadminsso`

4.  Pokud chcete nakonfigurovat aplikace **SP** initiated režimu proveďte následující kroky:

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte pomocí skutečného adresy URL identifikátor, adresa URL odpovědi a přihlášení. Obraťte se na [tým podpory Zscaler privátní přístup správce](https://help.zscaler.com/zpa-submit-ticket) k získání těchto hodnot.
 
5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. V okně prohlížeče jiný web a přihlášení do správce Zscaler privátní přístupu jako správce.

8. V horní části klikněte na tlačítko **správy** a přejděte do **ověřování** klikněte na část **IdP konfigurace**.

    ![Správce přístupu privátní Zscaler správce](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. V pravém horním rohu, klikněte na **Přidat konfiguraci IdP**. 

    ![Správce přístupu privátní Zscaler addidp](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Na **Přidat konfiguraci IdP** stránky proveďte následující kroky:
 
    ![Správce přístupu privátní Zscaler idpselect](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klikněte na tlačítko **vyberte soubor** nahrát stažený soubor metadat z Azure AD v **nahrát soubor metadat IdP** pole.

    b. Přečte **IdP metadata** z Azure AD a naplní všechny pole informace, jak je uvedeno níže.

    ![Správce přístupu privátní Zscaler idpconfig](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Vyberte **jednotné přihlašování** jako **správce**.

    d. Vyberte doménu z **domén** pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Vytvoření zkušebního uživatele Zscaler privátní přístup správce

Povolit uživatelům přihlášení k Zscaler privátní přístup správce Azure AD, se musí být zřízená do Zscaler privátní přístup správce. V případě správce přístupu privátní Zscaler zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Zscaler privátní přístup správce jako správce.

2. V horní části klikněte na tlačítko **správy** a přejděte do **ověřování** klikněte na část **IdP konfigurace**.

    ![Správce přístupu privátní Zscaler správce](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klikněte na tlačítko **správci** z levé strany v nabídce.

    ![Správce přístupu Zscaler privátní správce](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. V pravém horním rohu, klikněte na **přidat správce**:

    ![Správce přístupu Zscaler privátní přidat správce](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. V **přidat správce** proveďte následující kroky:

    ![Správce přístupu Zscaler privátní uživatel správce](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. V **uživatelské jméno** textovému poli, zadejte e-mailu uživatele jako **BrittaSimon@contoso.com**.

    b. V **heslo** textovému poli, zadejte heslo.

    c. V **Potvrdit heslo** textovému poli, zadejte heslo.

    d. Vyberte **Role** jako **Zscaler privátní přístup správce**.

    e. V **e-mailu** textovému poli, zadejte e-mailu uživatele jako **BrittaSimon@contoso.com**.

    f. V **Phone** textovému poli, zadejte telefonní číslo.

    g. V **časové pásmo** textovému poli, vyberte časové pásmo.

    h. Klikněte na **Uložit**.  

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k Zscaler privátní přístup správce.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon do modulu snap-in Správce přístupu privátní Zscaler, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscaler privátní přístup správce**.

    ![Správce přístupu Zscaler privátní propojení v seznamu aplikací](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zscaler privátní přístup správce na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Zscaler privátní přístup správce.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

