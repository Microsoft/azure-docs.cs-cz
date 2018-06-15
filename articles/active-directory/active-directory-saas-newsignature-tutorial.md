---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu správy cloudu Microsoft Azure | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a portálu pro správu cloudu Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 5dfd5377fa669322d0a61c71996badd45bc3d441
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345995"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Kurz: Integrace Azure Active Directory pomocí portálu správy cloudu Microsoft Azure

V tomto kurzu zjistěte, jak integrovat portál pro správu cloudu pro Microsoft Azure s Azure Active Directory (Azure AD).

Integrace portálu pro správu cloudu Microsoft Azure s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k portálu pro správu cloudu pro Microsoft Azure
- Můžete povolit uživatelům, aby automaticky získat přihlášení k portálu pro správu cloudu Microsoft Azure (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí portálu správy cloudu Microsoft Azure, potřebujete následující položky:

- Předplatné služby Azure AD
- Portál pro správu cloudu pro Microsoft Azure jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání portálu pro správu cloudu Microsoft Azure z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Přidání portálu pro správu cloudu Microsoft Azure z Galerie
Při konfiguraci integrace portálu pro správu cloudu Microsoft Azure do Azure AD, potřebujete přidat portál pro správu cloudu pro Microsoft Azure z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál pro správu cloudu pro Microsoft Azure z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **portál pro správu cloudu pro Microsoft Azure**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. Na panelu výsledků vyberte **portál pro správu cloudu pro Microsoft Azure**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí portálu správy cloudu Microsoft Azure na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v portálu pro správu cloudu Microsoft Azure je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské na portálu pro správu cloudu Microsoft Azure.

V portálu pro správu cloudu Microsoft Azure, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí portálu správy cloudu Microsoft Azure, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření portál pro správu cloudu pro Microsoft Azure testovacího uživatele](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  – Pokud chcete mít protějšek Britta Simon v portálu pro správu cloudu Microsoft Azure, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v portálu pro správu cloudu pro aplikaci Microsoft Azure.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí portálu správy cloudu Microsoft Azure, proveďte následující kroky:**

1. Na portálu Azure na **portál pro správu cloudu pro Microsoft Azure** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. Na **domény Microsoft Azure a adresy URL portálu pro správu cloudu** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování, identifikátor a adresa URL odpovědi. Obraťte se na [portál pro správu cloudu pro klienta Microsoft Azure tým podpory](mailto:jczernuszka@newsignature.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. Na **portál pro správu cloudu pro Microsoft Azure Configuration** klikněte na tlačítko **nakonfigurovat portál správy cloudu pro Microsoft Azure** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Konfigurace jednotného přihlašování na **portál pro správu cloudu pro Microsoft Azure** straně, budete muset odeslat stažené **certifikát**, **Sign-Out URL**, **SAML jeden přihlašování adresa URL služby** a **SAML Entity ID** k [portál pro správu cloudu pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Vytváření portál pro správu cloudu pro Microsoft Azure testovacího uživatele

Cílem této části je vytvoření uživatele volat Britta Simon na portálu pro správu cloudu pro Microsoft Azure. Spojte se s [portál pro správu cloudu pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com) přidání uživatelů v portálu pro správu cloudu pro účet Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k portálu pro správu cloudu Microsoft Azure.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon portál pro správu cloudu pro Microsoft Azure, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **portál pro správu cloudu pro Microsoft Azure**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.
Když kliknete na portálu pro správu cloudu pro dlaždice Microsoft Azure na přístupovém panelu, jste měli získat automaticky přihlášení k portálu pro správu cloudu pro aplikaci Microsoft Azure.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

