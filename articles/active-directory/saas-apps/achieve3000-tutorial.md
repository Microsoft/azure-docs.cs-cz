---
title: 'Kurz: Azure Active Directory integrace s Achieve3000 | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Achieve3000.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: b943a805a025610561f0369d6ce4277600391672
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215303"
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Kurz: Azure Active Directory integrace s Achieve3000

V tomto kurzu zjistěte, jak integrovat Achieve3000 s Azure Active Directory (Azure AD).

Integrace Achieve3000 s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Achieve3000.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Achieve3000 (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Achieve3000, potřebujete následující položky:

- Předplatné služby Azure AD
- Achieve3000 jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Achieve3000 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-achieve3000-from-the-gallery"></a>Přidání Achieve3000 z Galerie
Při konfiguraci integrace Achieve3000 do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Achieve3000 z galerie.

**Pokud chcete přidat Achieve3000 z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Achieve3000**, vyberte **Achieve3000** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Achieve3000 v seznamu výsledků](./media/achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Achieve3000 podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Achieve3000 je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Achieve3000 musí navázat.

V Achieve3000, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Achieve3000, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Achieve3000](#create-an-achieve3000-test-user)**  – Pokud chcete mít protějšek Britta Simon v Achieve3000 propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Achieve3000.

**Ke konfiguraci Azure AD jednotné přihlašování s Achieve3000, proveďte následující kroky:**

1. Na portálu Azure na **Achieve3000** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. Na **Achieve3000 domény a adresy URL** část, proveďte následující kroky:

    ![Achieve3000 domény a adresy URL jednotné přihlašování informace](./media/achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následující: vzoru: `https://saml.achieve3000.com/district/<District Identifier>`

    b. V **identifikátor** textovému poli, zadejte hodnotu: `achieve3000-saml`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory Achieve3000 klienta](https://www.achieve3000.com/contact-us/) k získání hodnoty. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. Aplikace Achieve3000 očekává, že jedinečný **studentID** hodnota v názvu identifikátor deklarace identity. Zákazníka můžete namapovat správné hodnoty pro název identifikátor deklarace identity. V takovém případě jsme jsou namapovány **user.mail** pro ukázku účel. Ale podle jedinečný identifikátor, by měla být mapována správnou hodnotu pro ni.   

    ![Konfigurovat jednotné přihlašování attb](./media/achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | studentID               | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování přidat](./media/achieve3000-tutorial/tutorial_officespace_04.png)

    ![Konfigurace Addattb přihlášení](./media/achieve3000-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/achieve3000-tutorial/tutorial_general_400.png)
    
8. Konfigurace jednotného přihlašování na **Achieve3000** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Achieve3000](https://www.achieve3000.com/contact-us/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/achieve3000-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/achieve3000-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/achieve3000-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/achieve3000-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-achieve3000-test-user"></a>Vytvořit uživatele s Achieve3000 testu

V této části vytvoříte volal Britta Simon v Achieve3000 uživatele. Práce s [tým podpory Achieve3000](https://www.achieve3000.com/contact-us/) přidat uživatele do Achieve3000 platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Achieve3000.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Achieve3000, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Achieve3000**.

    ![V seznamu aplikací na Achieve3000 odkaz](./media/achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Achieve3000 na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Achieve3000.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/achieve3000-tutorial/tutorial_general_203.png

