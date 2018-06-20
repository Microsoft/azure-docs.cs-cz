---
title: 'Kurz: Azure Active Directory integrace se sadou G Suite | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: ce1549231157712ff7421b85685ae796388e77f9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225091"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Kurz: Azure Active Directory integrace se sadou G Suite

V tomto kurzu zjistěte, jak integrovat G Suite s Azure Active Directory (Azure AD).

Integrace sady G s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k G Suite.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Suite G (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace Azure AD integrace se sadou G Suite, potřebujete následující položky:

- Předplatné služby Azure AD
- G Suite jednotné přihlašování povolené předplatné
- Google Apps předplatné nebo Google Cloud Platform předplatného.

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
1.  **Otázka: podporuje tato podpora integrace Google Cloud Platform jednotného přihlašování k integraci s Azure AD?**
    
    Odpověď: Ano. Google Cloud Platform a Google Apps sdílet stejnou platformu ověřování. To uděláte GCP integrace, je nutné nakonfigurovat jednotné přihlašování s Google Apps.


2. **Otázka: je Chromebooks a dalších zařízení Chrome kompatibilní s Azure AD jednotné přihlašování?**
   
    Odpověď: Ano, se uživatelé moct přihlásit k jejich Chromebook zařízení pomocí svých přihlašovacích údajů Azure AD. Toto [článek podpory od G Suite](https://support.google.com/chrome/a/answer/6060880) informace o důvod, proč může získat uživatelé vyzváni k zadání pověření dvakrát.

3. **Otázka: Pokud lze povolit jednotné přihlašování, budou uživatelé moci žádné Google produkt, například Google učebny, GMail, Google Drive, YouTube a tak dále se přihlaste pomocí přihlašovacích údajů Azure AD?**
   
    Odpověď: Ano, v závislosti na [které Suite G](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) rozhodnete povolit nebo zakázat pro vaši organizaci.

4. **Otázka: je možné povolit jednotné přihlašování pro pouze podmnožinu Moji uživatelé G Suite?**
   
    Odpověď: Ne, zapnout jednotné přihlašování okamžitě vyžaduje všichni uživatelé G Suite k ověření pomocí přihlašovacích údajů Azure AD. Protože G Suite nepodporuje, s více poskytovatelů identit, zprostředkovatele identity pro vaše prostředí G Suite může být buď Azure AD nebo Google – ale ne oba ve stejnou dobu.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím systému Windows, jsou že automaticky ověřují G Suite bez získávání výzva k zadání hesla?**
   
    Odpověď: existují dvě možnosti pro povolení tohoto scénáře. Nejdřív by uživatelé přihlašovat do zařízení s Windows 10 prostřednictvím [Azure Active Directory Join](../device-management-introduction.md). Alternativně může uživatelé přihlašovat do zařízení s Windows, které jsou připojené k doméně pro místní Active Directory byla povolena pro jednotné přihlašování do služby Azure AD pomocí [Active Directory Federation Services (AD FS)](../connect/active-directory-aadconnect-user-signin.md) nasazení. Obě možnosti vyžadují, abyste proveďte kroky v následujícím kurzu umožňující jednotného přihlašování mezi službou Azure AD a G Suite.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání G Suite z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie
Pokud chcete nakonfigurovat integraci sady G do služby Azure AD, potřebujete přidat G Suite z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat G Suite z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **G Suite**, vyberte **G Suite** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Sada G v seznamu výsledků](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s G Suite podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sadě G je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sadě G, je nutné stanovit.

V sadě G přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se sadou G Suite, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele G Suite](#create-a-g-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon sady G, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci G Suite.

**Ke konfiguraci Azure AD jednotné přihlašování se sadou G Suite, proveďte následující kroky:**

1. Na portálu Azure na **G Suite** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. Na **G Suite domény a adresy URL** část, pokud chcete nakonfigurovat pro **z Gmailu** proveďte následující kroky:

    ![G Suite domény a adresy URL jednotné přihlašování informace](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte URL adresu URL pomocí následujícího vzorce: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot.

4. Na **G Suite domény a adresy URL** část, pokud chcete nakonfigurovat pro **Google Cloud Platform** proveďte následující kroky:

    ![G Suite domény a adresy URL jednotné přihlašování informace](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte URL adresu URL pomocí následujícího vzorce: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikát** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/google-apps-tutorial/tutorial_general_400.png)

7. Na **G Suite konfigurace** klikněte na tlačítko **konfigurace Suite G** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML jeden přihlašování adresa URL služby a změnit heslo URL** z **Stručná referenční příručka části.**

    ![Konfigurace sady G](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

8. V prohlížeči otevřete novou kartu a přihlaste se k [konzoly pro správu sady G](http://admin.google.com/) pomocí účtu správce.

9. Klikněte na tlačítko **zabezpečení**. Pokud nevidíte odkaz, mohou být skryty pod **více ovládacích prvků** nabídky v dolní části obrazovky.
   
    ![Klikněte na Zabezpečení.][10]

10. Na **zabezpečení** klikněte na tlačítko **nastavit jednotné přihlašování (SSO).**
   
    ![Klikněte na tlačítko jednotné přihlašování.][11]

11. Proveďte následující změny konfigurace:
   
    ![Konfigurace jednotného přihlašování][12]
   
    a. Vyberte **nastavení jednotného přihlašování pomocí zprostředkovatele identity jiných výrobců**.

    b. V **přihlašovací adresa URL stránky** pole v sadě G, vložte hodnotu **jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací stránky** pole v sadě G, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure. 

    d. V **změnit heslo URL** pole v sadě G, vložte hodnotu **změnit heslo URL** který jste zkopírovali z portálu Azure. 

    e. V sadě G pro **ověřovací certifikát**, nahrajte certifikát, který jste si stáhli z portálu Azure.

    f. Vyberte **použít konkrétní vystavitele domény**.

    g. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/google-apps-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/google-apps-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/google-apps-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/google-apps-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-g-suite-test-user"></a>Vytvoření zkušebního uživatele G Suite

Cílem této části je vytvoření uživatele volal Britta Simon v G Suite softwaru. G Suite podporuje automatické zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná akce pro vás v této části. Pokud uživatel v softwaru Suite G ještě neexistuje, vytvoří se nový při pokusu o přístup k softwaru Suite G.

>[!NOTE] 
>Pokud je potřeba ručně vytvořit uživateli, obraťte se [tým podpory Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k G Suite.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon G Suite, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **G Suite**.

    ![V seznamu aplikací na odkaz G Suite](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici G Suite na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci G Suite.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

