---
title: 'Kurz: Azure Active Directory integrace s přenosy MOVEit – integrace Azure AD | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenos MOVEit – integrace Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: f9bfd3ad884ad36026d56af514391c5f75a97e51
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Azure Active Directory integrace s přenosy MOVEit – integrace Azure AD

V tomto kurzu zjistěte, jak integrovat MOVEit přenos – integrace Azure AD s Azure Active Directory (Azure AD).

Integrace MOVEit přenos – Azure AD integraci s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k MOVEit přenos – integrace Azure AD.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k MOVEit přenos – integrace Azure AD (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přenosy MOVEit – integrace služby Azure AD, potřebujete následující položky:

- Předplatné služby Azure AD
- Přenos MOVEit – Azure AD integrace jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání MOVEit přenos – integrace Azure AD z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání MOVEit přenos – integrace Azure AD z Galerie
Konfigurace integrace MOVEit přenos – integrace Azure AD do služby Azure AD, musíte přidat MOVEit přenos – Azure AD integrace z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat MOVEit přenos – integrace Azure AD z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **MOVEit přenos – integrace Azure AD**, vyberte **MOVEit přenos – integrace Azure AD** z panelu výsledků klikněte **přidat** tlačítko přidáte aplikace.

    ![Přenos MOVEit – integrace Azure AD v seznamu výsledků](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s přenosy MOVEit – integrace Azure AD na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v MOVEit přenos – integrace Azure AD je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v MOVEit přenos – integrace Azure AD je nutné stanovit.

V MOVEit přenos – integrace služby Azure AD, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přenosy MOVEit – integrace služby Azure AD, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření MOVEit přenos – Azure AD integrace testovacího uživatele](#create-a-moveit-transfer---azure-ad-integration-test-user)**  – Pokud chcete mít protějšek Britta Simon v MOVEit přenos – integrace Azure AD, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v MOVEit přenos – integrace aplikace Azure AD.

**Ke konfiguraci Azure AD jednotné přihlašování s přenosy MOVEit – integrace služby Azure AD, proveďte následující kroky:**

1. Na portálu Azure na **MOVEit přenos – integrace Azure AD** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Na **MOVEit přenos – integrace Azure AD domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://contoso.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://contoso.com/<tenatid>`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Najdete tyto hodnoty později v **adresa URL služby zprostředkovatele metadat** části nebo kontaktujte [MOVEit přenos – tým podpory pro Azure AD integrace klienta](https://community.ipswitch.com/s/support) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Přihlásit se jako správce klienta MOVEit přenosu.

7. V levém navigačním podokně klikněte na tlačítko **nastavení**.

    ![Nastavení oddílu na aplikaci straně](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Klikněte na tlačítko **jeden Sign-on** odkaz, což je pod **zásady zabezpečení -> Auth uživatele**.

    ![Zabezpečení straně zásady na aplikace](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Klikněte na adresu URL metadat odkaz ke stažení dokument metadat.

    ![Adresa URL služby zprostředkovatele metadat](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Ověřte **entityID** odpovídá **identifikátor** v **MOVEit přenos – integrace Azure AD domény a adresy URL** části.
    * Ověřte **AssertionConsumerService** umístění URL odpovídá **adresa URL odpovědi** v **MOVEit přenos – integrace Azure AD domény a adresy URL** části.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Klikněte na tlačítko **přidat zprostředkovatele Identity** tlačítko Přidat nového poskytovatele federovaných identit.

    ![Přidejte zprostředkovatele Identity](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Klikněte na tlačítko **Procházet...**  a vyberte soubor metadat, který jste si stáhli z portálu Azure, potom klikněte na **přidat zprostředkovatele Identity** nahrát stažený soubor.

    ![Poskytovatele Identity SAML](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Vyberte "**Ano**" jako **povoleno** v **Upravit federované nastavení zprostředkovatele Identity...**  a klikněte na tlačítko **Uložit**.

    ![Nastavení zprostředkovatele federovaných identit](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. V **Upravit federované Identity uživatele nastavení zprostředkovatele** proveďte následující akce:
    
    ![Upravit nastavení zprostředkovatele federovaných identit](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Vyberte **SAML NameID** jako **přihlašovací jméno**.
    
    b. Vyberte **jiných** jako **úplný název** a v **název atributu** vložte hodnotu do textového pole: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Vyberte **jiných** jako **e-mailu** a v **název atributu** vložte hodnotu do textového pole: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Vyberte **Ano** jako **automatické vytvoření účtu na Sign-on**.
    
    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Vytvoření MOVEit přenos – Azure AD integrace testovacího uživatele

Cílem této části je vytvoření uživatele volal Britta Simon v MOVEit přenos – integrace Azure AD. Přenos MOVEit – integrace Azure AD podporuje zřizování za běhu, které jste povolili. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k MOVEit přenos – integrace Azure AD, pokud ještě neexistuje.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [MOVEit přenos – tým podpory pro Azure AD integrace klienta](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu MOVEit přenos – integrace Azure AD.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit přenosy MOVEit – integrace služby Azure AD, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **MOVEit přenos – integrace Azure AD**.

    ![Přenos MOVEit – integrace Azure AD odkaz v seznamu aplikací](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Po kliknutí na tlačítko MOVEit přenos – integrace Azure AD dlaždici na přístupovém panelu jste měli získat automaticky přihlášení k MOVEit přenos – integrace aplikace Azure AD. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

