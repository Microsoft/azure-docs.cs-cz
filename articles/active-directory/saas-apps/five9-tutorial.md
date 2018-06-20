---
title: 'Kurz: Azure Active Directory integrace s Five9 Plus adaptér (CTI, obraťte se na centrum agenty) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Five9 Plus adaptér (CTI, obraťte se na centrum agenty).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: 1698a1fc6f9acc2815ae982e090651301d0a8bc5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220603"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Kurz: Azure Active Directory integrace s Five9 Plus adaptér (CTI, obraťte se na centrum agentů)

V tomto kurzu jste zjistěte, jak integrovat Five9 Plus adaptér (CTI, obraťte se na centrum agenty) s Azure Active Directory (Azure AD).

Integrace Five9 Plus adaptér (CTI, obraťte se na centrum agenty) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Five9 Plus adaptéru (CTI, obraťte se na centrum agentů)
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Five9 Plus adaptér (CTI, obraťte se na centrum agenty) (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Five9 Plus adaptér (CTI, obraťte se na centrum agenty), potřebujete následující položky:

- Předplatné služby Azure AD
- Five9 Plus adaptér (CTI, obraťte se na centrum agenty) jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat a jeden měsíc zkušební: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Five9 Plus adaptér (CTI, obraťte se na centrum agenty) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Přidání Five9 Plus adaptér (CTI, obraťte se na centrum agenty) z Galerie
Při konfiguraci integrace Five9 Plus adaptéru (CTI, obraťte se na centrum agenty) do služby Azure AD, musíte přidat Five9 Plus adaptér (CTI, obraťte se na centrum agenty) z Galerie si na seznam spravovaných aplikací SaaS.

**Přidání Five9 Plus adaptéru (CTI, obraťte se na centrum agenty) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Five9 Plus adaptér (CTI, obraťte se na centrum agenty)**.

    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/tutorial_five9_search.png)

5. Na panelu výsledků vyberte **Five9 Plus adaptér (CTI, obraťte se na centrum agenty)** a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování s Five9 Plus adaptér (CTI, obraťte se na centrum agenty) na základě testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Five9 Plus adaptéru (CTI, obraťte se na centrum agenty) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Five9 Plus adaptéru (CTI, obraťte se na centrum agenty).

V Five9 Plus adaptér (CTI, obraťte se na centrum agenty), přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, obraťte se na centrum agenty), je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Five9 Plus adaptér (CTI, obraťte se na centrum agenty)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  – Pokud chcete mít protějšek Britta Simon v Five9 Plus adaptéru (CTI, obraťte se na centrum agenty) propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Five9 Plus adaptér (CTI, obraťte se na centrum agenty).

**Ke konfiguraci Azure AD jednotné přihlašování s Five9 Plus adaptérem (CTI, obraťte se na centrum agenty), proveďte následující kroky:**

1. Na portálu Azure na **Five9 Plus adaptér (CTI, obraťte se na centrum agenty)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_five9_samlbase.png)

3. Na **Five9 Plus adaptér (CTI, obraťte se na centrum agenty) domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    |    Prostředí      |       zprostředkovatele identity      |
    | :-- | :-- |
    | Pro "Five9 Plus adaptér pro aplikaci Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Pro "Five9 Plus adaptér Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Pro "Five9 Plus adaptér plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    |      Prostředí     |      zprostředkovatele identity      |
    | :--                  | :--           |
    | Pro "Five9 Plus adaptér pro aplikaci Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Pro "Five9 Plus adaptér Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Pro "Five9 Plus adaptér plochy Toolkit agenta" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_five9_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_general_400.png)

6. Na **konfigurace Five9 Plus adaptéru (CTI, obraťte se na centrum agenty)** klikněte na tlačítko **konfigurace Five9 Plus adaptér (CTI, obraťte se na centrum agenty)** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_five9_configure.png) 

7. Konfigurace jednotného přihlašování na **Five9 Plus adaptér (CTI, obraťte se na centrum agenty)** straně, budete muset odeslat stažené **Certificate(Base64), adresa URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory Five9 Plus adaptér (CTI, obraťte se na centrum agenty)](https://www.five9.com/about/contact). Kromě toho také další konfigurace jednotného přihlašování k postupujte podle níže uvedených pokynů podle adaptéru:

    a. "Five9 Plus adaptér plochy Toolkit agenta" Příručka správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adaptér pro aplikaci Microsoft Dynamics CRM" Příručka správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adaptér Zendesk" Příručka správce: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/five9-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Vytvoření zkušebního uživatele Five9 Plus adaptér (CTI, obraťte se na centrum agentů)

V této části vytvoříte uživatele názvem Britta Simon v Five9 Plus adaptéru (CTI, obraťte se na centrum agenty). Práce s [tým podpory Five9 Plus adaptér (CTI, obraťte se na centrum agenty)](https://www.five9.com/about/contact) přidat uživatele do platformy Five9 Plus adaptér (CTI, obraťte se na centrum agenty). Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k Five9 Plus adaptéru (CTI, obraťte se na centrum agenty).

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Five9 Plus adaptér (CTI, obraťte se na centrum agenty), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Five9 Plus adaptér (CTI, obraťte se na centrum agenty)**.

    ![Konfigurovat jednotné přihlašování](./media/five9-tutorial/tutorial_five9_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Five9 Plus adaptér (CTI, obraťte se na centrum agenty) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Five9 Plus adaptér (CTI, obraťte se na centrum agenty).
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

