---
title: 'Kurz: Azure Active Directory integrace s Dropbox pro firmy | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropbox pro firmy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: a0481b2eb688b70d5e56b2b6793b026d6b4c96a2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219995"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Kurz: Azure Active Directory integrace s Dropbox pro firmy

V tomto kurzu zjistěte, jak integrovat Dropbox pro firmy s Azure Active Directory (Azure AD).

Integrace Dropbox pro firmy s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Dropboxu pro firmy.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Dropboxu pro firmy (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Dropbox pro firmy, potřebujete následující položky:

- Předplatné služby Azure AD
- Dropbox pro obchodní jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Dropbox pro firmy z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Přidání Dropbox pro firmy z Galerie
Pokud chcete nakonfigurovat integraci Dropbox pro firmy do služby Azure AD, potřebujete přidat Dropbox pro firmy z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Dropbox pro firmy z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Dropbox pro firmy**, vyberte **Dropbox pro firmy** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Dropbox pro firmy v seznamu výsledků](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s Dropbox pro firmy na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Dropbox pro firmy je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Dropbox pro firmy.

V Dropbox pro firmy, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Dropbox pro firmy, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření Dropbox pro obchodní testovacího uživatele](#create-a-dropbox-for-business-test-user)**  – Pokud chcete mít protějšek Britta Simon v Dropbox pro podnikání, které je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování do vaší schránky pro obchodní aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s Dropbox pro firmy, proveďte následující kroky:**

1. Na portálu Azure na **Dropbox pro firmy** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Na **Dropbox obchodní domény a adresy URL** část, proveďte následující kroky:

    ![Dropbox obchodní domény a adresy URL jeden přihlašování informace](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.dropbox.com/sso/<id>`

    b. V **identifikátor** textovému poli, typ a hodnotu: `Dropbox`

    > [!NOTE] 
    > Předchozí hodnota přihlašovací adresa URL není skutečné hodnoty. Aktualizujte hodnotu s skutečné přihlašování adresy URL, který je vysvětlen později v tomto kurzu. Obraťte se na [Dropbox pro klienta obchodní tým podpory](https://www.dropbox.com/business/contact) k získání hodnoty. 
 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

6. Na **Dropbox pro konfiguraci obchodní** klikněte na tlačítko **Dropbox nakonfigurovat pro firmy** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Dropbox obchodní konfigurace](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Konfigurace jednotného přihlašování na **Dropbox pro firmy** straně, přejděte na vaší schránky pro obchodní klienta.

    a. Přihlásit se do vaší schránky pro obchodní klienta. 
   
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/ic769509.png "nakonfigurovat jednotné přihlašování")
   
    b. V navigačním podokně na levé straně klikněte na **konzoly pro správu**. 
   
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/ic769510.png "nakonfigurovat jednotné přihlašování")
   
    c. Na **konzoly pro správu**, klikněte na tlačítko **ověřování** v levém navigačním podokně. 
   
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/ic769511.png "nakonfigurovat jednotné přihlašování")
   
    d. V **jednotného přihlašování** vyberte **povolit jednotné přihlašování**a potom klikněte na **Další** rozbalte v této části.  
   
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/ic769512.png "nakonfigurovat jednotné přihlašování")
   
    e. Zkopírujte adresu URL do **uživatelé se mohou přihlásit zadáním e-mailové adresy nebo můžete přejít přímo na** a vložte ji do **přihlašovací adresa URL** textbox z **Dropbox obchodní domény a adresy URL** části na portálu Azure. 
    
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/ic769513.png)
    
8. V **jednotného přihlašování** části **ověřování** proveďte následující kroky: 
   
    ![Konfigurovat jednotné přihlašování](./media/dropboxforbusiness-tutorial/IC769516.png "nakonfigurovat jednotné přihlašování")
   
    a. Klikněte na tlačítko **požadované**.
   
    b. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. Klikněte na tlačítko **vyberte certifikát**a pak přejděte do vaší **Base64 kódovaného certifikátu souboru**.

    d. Klikněte na tlačítko **uložit změny** k dokončení konfigurace na vaší schránky pro obchodní klienta.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Vytvoření Dropbox pro obchodní testovacího uživatele

V této části se uživatel volá Britta Simon vytvoří v Dropbox pro firmy. Dropbox pro firmy podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Pokud uživatel v Dropbox pro firmy ještě neexistuje, vytvoří se nový při pokusu o přístup k Dropboxu pro firmy.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [Dropbox pro tým podpory obchodní klienta](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Dropbox pro firmy.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Dropbox pro firmy, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Dropbox pro firmy**.

    ![Dropbox pro obchodní odkaz v seznamu aplikací](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Dropbox pro obchodní dlaždici na přístupovém panelu, měli byste obdržet přihlašovací stránku vaší schránky pro obchodní aplikace.
 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

