---
title: 'Kurz: Azure Active Directory integrace se sítěmi Palo Alto - další zpracování portálu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - další zpracování portálu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aaa2adc30d1d798312b50370d4b635d0d8a123e9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347410"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Kurz: Azure Active Directory integrace se sítěmi Palo Alto - další zpracování portálu

V tomto kurzu zjistěte, jak integrovat Palo Alto Networks - další zpracování portálu v Azure Active Directory (Azure AD).

Integrace Palo Alto sítí - další zpracování portálu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k sítím Palo Alto - další zpracování portálu.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k sítím Palo Alto - další zpracování portálu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se sítěmi Palo Alto - další zpracování portál, potřebujete následující položky:

- Předplatné služby Azure AD
- Palo Alto sítě - další zpracování portál jednotné přihlašování povoleno předplatného

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Palo Alto sítě - další zpracování portálu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Přidání Palo Alto sítě - další zpracování portálu z Galerie
Při konfiguraci integrace Palo Alto sítí - další zpracování portálu do služby Azure AD, potřebujete přidat Palo Alto Networks - další zpracování portál z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - další zpracování portál z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Palo Alto Networks - další zpracování portál**, vyberte **Palo Alto Networks - další zpracování portál** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci .

    ![Palo Alto sítě - další zpracování portálu v seznamu výsledků](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování se sítěmi Palo Alto - další zpracování portál podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sítích Palo Alto - další zpracování portál je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sítích Palo Alto - další zpracování portál musí navázat.

V sítích Palo Alto - další zpracování portál přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se sítěmi Palo Alto - další zpracování portál, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořte sítě Palo Alto - další zpracování portál testovacího uživatele](#create-a-palo-alto-networks---captive-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon v sítích Palo Alto - další zpracování portál, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování ve svých sítích Palo Alto - aplikace portálu další zpracování.

**Ke konfiguraci Azure AD jednotné přihlašování se sítěmi Palo Alto - další zpracování portál, proveďte následující kroky:**

1. Na portálu Azure na **Palo Alto Networks - další zpracování portál** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

3. Na **Palo Alto sítě - další zpracování portál domény a adresy URL** část, proveďte následující kroky:

    ![Palo Alto sítě - další zpracování portál domény a adresy URL jeden přihlašování informace](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [Palo Alto Networks - tým podpory pro další zpracování portál](https://support.paloaltonetworks.com/support) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

6. Otevřete web Palo Alto jako správce v jiném okně prohlížeče.

7. Klikněte na **zařízení**.

    ![Konfigurace Palo Alto jednotné přihlašování](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Vyberte **poskytovatele Identity SAML** z levém navigačním panelu a klikněte na tlačítko "Import" k importu souboru metadat.

    ![Konfigurace Palo Alto jednotné přihlašování](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Proveďte následující akce v okně Import

    ![Konfigurace Palo Alto jednotné přihlašování](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. V **název profilu** textovému poli, zadejte název např uživatelského rozhraní správce Azure AD.
    
    b. V **metadat zprostředkovatelů Identity**, klikněte na tlačítko **Procházet** a vyberte soubor metadata.xml, který jste si stáhli z portálu Azure
    
    c. Klikněte na tlačítko **OK**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Vytvořte sítě Palo Alto - další zpracování portál testovacího uživatele

Cílem této části je vytvoření uživatele volat Britta Simon v sítích Palo Alto - další zpracování portálu. Palo Alto sítě - další zpracování portál podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k sítím Palo Alto - další zpracování portálu, pokud ještě neexistuje. 

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [Palo Alto Networks - tým podpory pro další zpracování portál](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k sítím Palo Alto - další zpracování portál Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit Palo Alto Networks - další zpracování portál, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Palo Alto Networks - další zpracování portál**.

    ![Sítě Palo Alto - další zpracování portál odkaz v seznamu aplikací](./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Další zpracování Portal je nakonfigurován za bránou firewall na virtuální počítač s Windows.  Testovat jednotného přihlašování na portálu. Další zpracování, přihlášení do systému Windows virtuálního počítače pomocí protokolu RDP. V této relaci RDP, otevřete prohlížeč na libovolný web, by měl automaticky otevřít adresu url jednotné přihlašování a řádku pro ověřování. Po dokončení ověřování byste měli mít k navgiate na webové servery. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

