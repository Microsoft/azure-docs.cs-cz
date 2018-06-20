---
title: 'Kurz: Azure Active Directory integrace s aplikace finančních prostředků Portal | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a portálu finančních prostředků.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4663cc8a-976a-4c6c-b3b4-1e5df9b66744
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 5e6707e8f537e173a851e575a5c32622951dda99
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222683"
---
# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Kurz: Azure Active Directory integrace s aplikace Portal finančních prostředků

V tomto kurzu zjistěte, jak integrovat aplikace Portal finančních prostředků s Azure Active Directory (Azure AD).

Integrace aplikace Portal finančních prostředků s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k portálu finančních prostředků
- Můžete povolit uživatelům, aby automaticky získat přihlášení k aplikace finančních prostředků Portal (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí portálu finančních prostředků, potřebujete následující položky:

- Předplatné služby Azure AD
- Portálu finančních prostředků jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání aplikace Portal finančních prostředků z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-the-funding-portal-from-the-gallery"></a>Přidání aplikace Portal finančních prostředků z Galerie
Konfigurace integrace portál finančních prostředků do Azure AD, potřebujete přidat portálu finančních prostředků z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat aplikace Portal finančních prostředků z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **aplikace finančních prostředků Portal**.

    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/tutorial_thefundingportal_search.png)

5. Na panelu výsledků vyberte **aplikace finančních prostředků Portal**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/tutorial_thefundingportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí aplikace Portal finančních prostředků na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem portálu finančních prostředků je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské portálu finančních prostředků je potřeba vytvořit.

Portálu finančních prostředků, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování portálu finančních prostředků, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele portálu finančních prostředků](#creating-the-funding-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon v The finančních prostředků portál, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci portálu finančních prostředků.

**Ke konfiguraci Azure AD jednotné přihlašování portálu finančních prostředků, proveďte následující kroky:**

1. Na portálu Azure na **aplikace finančních prostředků Portal** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_samlbase.png)

3. Na **doméně prostředků portál a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.regenteducation.net/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.regenteducation.net`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory finančních prostředků klient portál](mailto:info@regenteducation.com) k získání těchto hodnot. 

4. Aplikace z portálu finančních prostředků očekává kontrolní výrazy SAML obsahuje atribut s názvem "externalId1". Hodnota "externalId1" by měl být rozpoznaný studentID. Konfigurace deklarace identity "externalId1" pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **uživatelské atributy** aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_attribute.png)

5. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ------------------- | ---------------- |
    | externalId1 | User.extensionattribute1 |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnota atributu** vyberte atribut, který chcete použít týkající se vaší implementace. Například pokud je hodnota StudentID mít uložen v ExtensionAttribute1, pak vyberte user.extensionattribute1.
    
    d. Klikněte na tlačítko **OK**.
 
6. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_general_400.png)

8. Konfigurace jednotného přihlašování na **aplikace finančních prostředků Portal** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory aplikace finančních prostředků Portal](mailto:info@regenteducation.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/thefundingportal-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-the-funding-portal-test-user"></a>Vytvoření aplikace finančních prostředků Portal testovacího uživatele

V této části vytvoříte uživatele volat Britta Simon portálu finančních prostředků. Práce s [tým podpory aplikace Portal finančních prostředků](mailto:info@regenteducation.com) k přidání testovacího uživatele a povolení jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu aplikace Portal finančních prostředků.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon aplikace Portal finančních prostředků, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **aplikace finančních prostředků Portal**.

    ![Konfigurovat jednotné přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici aplikace finančních prostředků Portal na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci portálu finančních prostředků.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/thefundingportal-tutorial/tutorial_general_04.png

[100]: ./media/thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/thefundingportal-tutorial/tutorial_general_201.png
[202]: ./media/thefundingportal-tutorial/tutorial_general_202.png
[203]: ./media/thefundingportal-tutorial/tutorial_general_203.png

