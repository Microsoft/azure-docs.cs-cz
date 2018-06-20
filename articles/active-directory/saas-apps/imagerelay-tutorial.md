---
title: 'Kurz: Azure Active Directory integrace s Image předávání | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a předávací bitové kopie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 1932980e1dd2f9de5ddb7ea3e77acb2a9f85fad5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225343"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Kurz: Azure Active Directory integrace s předávání bitové kopie

V tomto kurzu zjistěte, jak integrovat předávání bitové kopie s Azure Active Directory (Azure AD).

Integrace předávání bitové kopie s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k předávání bitové kopie
- Můžete povolit uživatelům, aby automaticky získat přihlášení k předávání bitové kopie (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s předávání bitové kopie, potřebujete následující položky:

- Předplatné služby Azure AD
- Předávání Image jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání bitové kopie předávání z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-image-relay-from-the-gallery"></a>Přidání bitové kopie předávání z Galerie
Při konfiguraci integrace předávání bitové kopie do služby Azure AD potřebujete přidat bitovou kopii předávání z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat bitovou kopii předávání z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Image předávání**.

    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

5. Na panelu výsledků vyberte **Image předávání**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s předávání bitové kopie založené na testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku Relay bitové kopie je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Image Relay musí navázat.

Obrázek Relay přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s předávání bitové kopie, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele předávání Image](#creating-an-image-relay-test-user)**  – Pokud chcete mít protějšek Britta Simon Relay bitové kopie, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci předávání bitové kopie.

**Ke konfiguraci Azure AD jednotné přihlašování s předávání bitové kopie, proveďte následující kroky:**

1. Na portálu Azure na **Image předávání** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. Na **Image předávání domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.imagerelay.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Image předávání klienta](http://support.imagerelay.com/) k získání těchto hodnot. 
 


4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_general_400.png)

6. Na **konfigurace přenosového Image** klikněte na tlačítko **konfigurace přenosového bitové kopie** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresa URL služby a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. V jiném okně prohlížeče Přihlaste se k serveru vaší společnosti předávání bitové kopie jako správce.

8. Na panelu nástrojů v horní části klikněte **uživatelé a oprávnění** zatížení.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Klikněte na tlačítko **vytvořit nová položka oprávnění**.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

10. V **jeden znak v nastavení** úlohy, vyberte **tato skupina může pouze přihlášení přes jednotné přihlašování** zaškrtněte políčko a potom klikněte na **Uložit**.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Přejděte na **nastavení účtu**.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Přejděte na **jeden znak v nastavení** zatížení.
    
     ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

13. Na **SAML nastavení** dialogové okno, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. V **přihlašovací adresa URL** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    b. V **adresy URL odhlašovací** textovému poli, vložte hodnotu **jednu adresu URL služby Sign-Out** který jste zkopírovali z portálu Azure.

    c. Jako **formát Id názvu**, vyberte **urn: oasis: názvy: tc: SAML:1.1:nameid-formátu: emailAddress**.

    d. Jako **vazby možnosti pro žádosti od poskytovatele služeb (Image relé)**, vyberte **POST vazby**.

    e. V části **x.509 Certificate**, klikněte na tlačítko **aktualizace certifikátu**.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. V poznámkovém bloku otevřete stažený certifikát, kopírovat obsah a pak ji vložit do textového pole certifikátu x.509.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. V **zřizování uživatelů JIT** vyberte **povolit zřizování uživatelů JIT**.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Vyberte skupinu oprávnění (například **jednotného přihlašování k základní**) povolený se přihlásit pouze pomocí jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-image-relay-test-user"></a>Vytváření testovacího uživatele předávání bitové kopie

Cílem této části je vytvoření uživatele volat Britta Simon Relay bitové kopie.

**Pokud chcete vytvořit uživateli volat Britta Simon Relay bitové kopie, proveďte následující kroky:**

1. Přihlášení k serveru vaší společnosti předávání bitové kopie jako správce.

2. Přejděte na **uživatelé a oprávnění** a vyberte **vytvořit jednotné přihlašování uživatele**.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Zadejte **e-mailu**, **křestní jméno**, **příjmení**, a **společnosti** chcete zřídit a vyberte skupinu oprávnění (pro uživatele Příklad: základní jednotné přihlašování) kterého je skupina, která může přihlásit pouze pomocí jednotného přihlašování.
   
    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Klikněte na možnost **Vytvořit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k předávání bitové kopie.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Relay bitové kopie, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Image předávání**.

    ![Konfigurovat jednotné přihlašování](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.    

Když kliknete na dlaždici předávání bitové kopie na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci předávání bitové kopie.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

