---
title: 'Kurz: Azure Active Directory integrace s Citrix ShareFile | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: f633206c03a9639a375535ed7c8f5c84aa334ebf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Kurz: Azure Active Directory integrace s Citrix ShareFile

V tomto kurzu zjistěte, jak integrovat Citrix ShareFile s Azure Active Directory (Azure AD).

Integrace Citrix ShareFile s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Citrix ShareFile.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k systému Citrix ShareFile (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadované součásti

Konfigurace integrace Azure AD s Citrix ShareFile, potřebujete následující položky:

- Předplatné služby Azure AD
- Citrix ShareFile jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat Citrix ShareFile z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-citrix-sharefile-from-the-gallery"></a>Přidat Citrix ShareFile z Galerie
Při konfiguraci integrace Citrix ShareFile do služby Azure AD potřebujete přidat Citrix ShareFile z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Citrix ShareFile z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Citrix ShareFile**, vyberte **Citrix ShareFile** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Citrix ShareFile v seznamu výsledků](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Citrix ShareFile podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Citrix ShareFile je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Citrix ShareFile musí navázat.

V systému Citrix ShareFile přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Citrix ShareFile, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  – Pokud chcete mít protějšek Britta Simon v Citrix ShareFile, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Citrix ShareFile.

**Ke konfiguraci Azure AD jednotné přihlašování s Citrix ShareFile, proveďte následující kroky:**

1. Na portálu Azure na **Citrix ShareFile** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. Na **Citrix ShareFile domény a adresy URL** část, proveďte následující kroky:

    ![Citrix ShareFile domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenant-name>.sharefile.com/saml/login`

    b. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [tým podpory Citrix ShareFile klienta](https://www.citrix.co.in/products/sharefile/support.html) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. Na **ShareFile konfigurace systému Citrix** klikněte na tlačítko **konfigurace Citrix ShareFile** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace systému Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png)

7. V okně prohlížeče jiný web, přihlaste se k vaší **Citrix ShareFile** společnosti lokality jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **správce**.

9. V levém navigačním podokně, vyberte **nakonfigurovat jednotné přihlašování**.
   
    ![Účet správy](./media/active-directory-saas-sharefile-tutorial/ic773627.png "účet správy")

10. Na **jednotného přihlašování nebo konfigurace SAML 2.0** dialogové okno stránky v rámci **základní nastavení**, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/active-directory-saas-sharefile-tutorial/ic773628.png "jednotného přihlašování")
   
    a. Klikněte na tlačítko **povolit SAML**.
    
    b. V **vaše Vystavitel deklarací identity nebo Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.

    c. Klikněte na tlačítko **změnu** vedle **certifikát X.509** pole a pak nahrajte certifikát jste si stáhli z portálu Azure.
    
    d. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
    
    e. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

11. Klikněte na tlačítko **Uložit** na portálu pro správu Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Vytvoření zkušebního uživatele Citrix ShareFile

Pokud chcete povolit uživatelům Azure AD přihlášení do systému Citrix ShareFile, musí být zřízená do Citrix ShareFile. V případě Citrix ShareFile zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Citrix ShareFile** klienta.

2. Klikněte na tlačítko **Správa uživatelů \> spravovat uživatelé domovské \> + vytvořit zaměstnanec**.
   
   ![Vytvoření zaměstnance](./media/active-directory-saas-sharefile-tutorial/IC781050.png "vytvoření zaměstnance")

3. Na **základní informace** část, proveďte následující kroky:
   
   ![Základní informace](./media/active-directory-saas-sharefile-tutorial/IC799951.png "základní informace")
   
   a. V **e-mailovou adresu** textovému poli, zadejte e-mailovou adresu Britta Simon jako **brittasimon@contoso.com**.
   
   b. V **křestní jméno** textovému poli, typ **křestní jméno** uživatele jako **Britta**.
   
   c. V **příjmení** textovému poli, typ **příjmení** uživatele jako **Simon**.

4. Klikněte na tlačítko **přidat uživatele**.
  
   >[!NOTE]
   >Držitel účtu Azure AD budou dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní. Další nástroje pro tvorbu účet uživatele systému Citrix ShareFile nebo rozhraní API poskytovaných Citrix ShareFile můžete použít ke zřízení uživatelských účtů Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Citrix ShareFile.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Citrix ShareFile, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Citrix ShareFile**.

    ![V seznamu aplikací na odkaz Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Citrix ShareFile na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Citrix ShareFile.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
