---
title: 'Kurz: Azure Active Directory integrace s Intralinks | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Intralinks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: f30f5619e71a529a5c9b321646ee7c6e0ffe79e8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901651"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Kurz: Azure Active Directory integrace s Intralinks

V tomto kurzu zjistěte, jak integrovat Intralinks s Azure Active Directory (Azure AD).

Integrace Intralinks s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Intralinks
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Intralinks (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Intralinks, potřebujete následující položky:

- Předplatné služby Azure AD
- Intralinks jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Intralinks z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-intralinks-from-the-gallery"></a>Přidání Intralinks z Galerie
Při konfiguraci integrace Intralinks do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Intralinks z galerie.

**Pokud chcete přidat Intralinks z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Intralinks**.

    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

5. Na panelu výsledků vyberte **Intralinks**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intralinks podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Intralinks je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Intralinks musí navázat.

V Intralinks, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intralinks, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele Intralinks](#creating-an-intralinks-test-user)**  – Pokud chcete mít protějšek Britta Simon v Intralinks propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Intralinks.

**Ke konfiguraci Azure AD jednotné přihlašování s Intralinks, proveďte následující kroky:**

1. Na portálu Azure na **Intralinks** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

3. Na **Intralinks domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:  `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Intralinks klienta](https://www.intralinks.com/contact-1) získat tuto hodnotu. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Intralinks** straně, budete muset odeslat stažené **soubor XML s metadaty** [tým podpory Intralinks](https://www.intralinks.com/contact-1). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-intralinks-test-user"></a>Vytváření testovacího uživatele Intralinks

V této části vytvoříte volal Britta Simon v Intralinks uživatele. Spojte se s [tým podpory Intralinks](https://www.intralinks.com/contact-1) přidat uživatele do Intralinks platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Intralinks.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Intralinks, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Intralinks**.

    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="add-intralinks-via-or-elite-application"></a>Přidání Intralinks prostřednictvím nebo Elite aplikace

Intralinks používá stejnou identitu platformy jednotného přihlašování pro všechny ostatní aplikace Intralinks s výjimkou pozornosti Nexus aplikace. Takže pokud máte v úmyslu použít žádnou jinou aplikaci Intralinks pak nejprve budete muset nakonfigurovat jednotné přihlašování pro jednu primární Intralinks aplikaci pomocí výše uvedený postup.

Potom můžete provést následující postup pro přidání jiná aplikace Intralinks ve vašem klientovi, který můžete využít této primární aplikace pro jednotné přihlašování. 

>[!NOTE]
>Tato funkce je k dispozici pouze zákazníkům SKU služby Azure AD Premium a není dostupné pro zákazníky volné nebo základní SKU.

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]


2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Intralinks**.

    ![Vytváření testovacího uživatele Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

5. Na **Intralinks přidat aplikaci** proveďte následující kroky:

    ![Přidání Intralinks prostřednictvím nebo Elite aplikace](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. V **název** textovému poli, zadejte odpovídající název aplikace, například **Intralinks Elite**.

    b. Klikněte na tlačítko **přidat** tlačítko.

6.  Na portálu Azure na **Intralinks** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

7. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **propojené přihlášení**.
 
    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

8. Získat SP iniciované URL jednotného přihlašování z [Intralinks team](https://www.intralinks.com/contact-1) pro jinou aplikaci Intralinks a zadejte ho v **konfigurovat přihlašovací adresa URL** jak je uvedeno níže. 
    
     ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     Do textového pole Přihlašovací adresa URL zadejte adresu URL používá uživatelům přihlášení do aplikace Intralinks pomocí následujícího vzorce:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

9. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/intralinks-tutorial/tutorial_general_400.png)

10. Přiřazení aplikace pro uživatele nebo skupiny, jak je uvedeno v části  **[přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Intralinks na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Intralinks.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png

