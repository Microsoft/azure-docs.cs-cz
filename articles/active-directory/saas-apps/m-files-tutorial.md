---
title: 'Kurz: Integrace Azure Active Directory s M-Files | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a M-Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: b5ab389feb1219d2282aa6bb3bb37077132a8779
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825778"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Kurz: Integrace Azure Active Directory s M-Files

V tomto kurzu se dozvíte, jak integrovat M-Files s Azure Active Directory (Azure AD).

M-Files integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k souborům M
- Můžete povolit uživatelům, aby automaticky získat přihlášení k M-Files (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s M-Files, potřebujete následující položky:

- Předplatné Azure AD
- M-Files jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání M-Files z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-m-files-from-the-gallery"></a>Přidání M-Files z Galerie
Konfigurace integrace M-Files do služby Azure AD, budete muset přidat M-Files v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat M-Files z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **M-Files**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/tutorial_m-files_search.png)

1. Na panelu výsledků vyberte **M-Files**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí M-Files podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v M-Files je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v M-Files musí být vytvořeno.

V M-Files, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s M-Files, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele M-Files](#creating-a-m-files-test-user)**  – Pokud chcete mít protějšek Britta Simon v M-Files, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci M-Files.

**Ke konfiguraci Azure AD jednotné přihlašování s M-Files, proveďte následující kroky:**

1. Na webu Azure Portal na **M-Files** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m-files_samlbase.png)

1. Na **M-Files domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m-files_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory M-Files klienta](mailto:support@m-files.com) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m-files_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_general_400.png)

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, kontaktujte [tým podpory M-Files](mailto:support@m-files.com) a poskytování přesných stažené metadat.
   
    >[!NOTE]
    >Pokud chcete nakonfigurovat jednotné přihlašování pro vás aplikace klasické pracovní plochy M souborů, postupujte podle dalších kroků. Žádné další kroky jsou povinné, pokud chcete nakonfigurovat jednotné přihlašování pro M-Files Webová verze.  

1. Postupujte podle dalších kroků při konfiguraci aplikace klasické pracovní plochy M souboru chcete povolit jednotné přihlašování s Azure AD. Chcete-li stáhnout M-Files, přejděte na [stáhnout M-Files](https://www.m-files.com/en/download-latest-version) stránky.

1. Otevřít **nastavení plochy M-Files** okna. Potom klikněte na **přidat**.
   
    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m_files_10.png)

1. Na **vlastnosti připojení dokumentu trezoru** okno, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m_files_11.png)  

    V rámci serveru části typ, hodnoty následujícím způsobem:  

    a. Pro **název**, typ `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Pro **číslo portu**, typ **4466**. 

    c. Pro **protokol**vyberte **HTTPS**. 

    d. V **ověřování** pole, vyberte **konkrétní Windows uživatele**. Potom zobrazí se výzva s stránku podpisování. Vložte přihlašovací údaje Azure AD. 

    e. Pro **úložiště na serveru**, vyberte odpovídající úložiště na serveru.
 
    f. Klikněte na **OK**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/m-files-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-m-files-test-user"></a>Vytvoření zkušebního uživatele M-Files

Cílem této části je vytvořte uživatele Britta Simon v M-Files. Práce s [tým podpory M-Files](mailto:support@m-files.com) k přidání uživatelů do M-Files.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k M-Files.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit M-Files, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **M-Files**.

    ![Konfigurace jednotného přihlašování](./media/m-files-tutorial/tutorial_m-files_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici M-Files na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci M-Files.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/m-files-tutorial/tutorial_general_01.png
[2]: ./media/m-files-tutorial/tutorial_general_02.png
[3]: ./media/m-files-tutorial/tutorial_general_03.png
[4]: ./media/m-files-tutorial/tutorial_general_04.png

[100]: ./media/m-files-tutorial/tutorial_general_100.png

[200]: ./media/m-files-tutorial/tutorial_general_200.png
[201]: ./media/m-files-tutorial/tutorial_general_201.png
[202]: ./media/m-files-tutorial/tutorial_general_202.png
[203]: ./media/m-files-tutorial/tutorial_general_203.png

