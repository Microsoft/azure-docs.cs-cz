---
title: 'Kurz: Azure Active Directory integrace s Lesson.ly | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lesson.ly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: fa52e444dae922c07ce4c1abf191342c66d9f223
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210305"
---
# <a name="tutorial-azure-active-directory-integration-with-lessonly"></a>Kurz: Azure Active Directory integrace s Lesson.ly

V tomto kurzu zjistěte, jak integrovat Lesson.ly s Azure Active Directory (Azure AD).

Integrace Lesson.ly s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Lesson.ly
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Lesson.ly (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Lesson.ly, potřebujete následující položky:

- Předplatné služby Azure AD
- Lesson.ly jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Lesson.ly z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lessonly-from-the-gallery"></a>Přidání Lesson.ly z Galerie
Při konfiguraci integrace Lesson.ly do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Lesson.ly z galerie.

**Pokud chcete přidat Lesson.ly z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Lesson.ly**.

    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/tutorial_lesson.ly_search.png)

5. Na panelu výsledků vyberte **Lesson.ly**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/tutorial_lesson.ly_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Lesson.ly podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Lesson.ly je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Lesson.ly musí navázat.

V Lesson.ly, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Lesson.ly, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Lesson.ly](#creating-a-lessonly-test-user)**  – Pokud chcete mít protějšek Britta Simon v Lesson.ly propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Lesson.ly.

**Ke konfiguraci Azure AD jednotné přihlašování s Lesson.ly, proveďte následující kroky:**

1. Na portálu Azure na **Lesson.ly** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lesson.ly_samlbase.png)

3. Na **Lesson.ly domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lesson.ly_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.lesson.ly/signin`|
    | `https://<companyname>.lessonly.com/signin`|

    >[!NOTE]
    >Při odkazování na obecný názvů, které **NázevSpolečnosti** potřebuje nahradit skutečným názvem.
    
    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |--|
    | `https://<companyname>.lesson.ly/auth/saml/metadata`|
    | `https://<companyname>.lessonly.com/auth/saml/metadata`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Lesson.ly klienta](mailto:dev@lessonly.com) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lesson.ly_certificate.png)

5. Aplikace Lesson.ly očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu SAML** konfigurace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lessonly_06.png)
           
6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:

    | Název atributu   | Hodnota atributu |
    | ---------------  | ----------------|
    | název urn: oid:2.5.4.42 |user.givenname |
    | název urn: oid:2.5.4.4  |user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 |User.Mail |
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.10 |User.ObjectId |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.     

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_general_400.png)

8. Na **Lesson.ly konfigurace** klikněte na tlačítko **konfigurace Lesson.ly** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lesson.ly_configure.png)

9. Konfigurace jednotného přihlašování na **Lesson.ly** straně, budete muset odeslat stažené **Certificate(Base64)** a **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory Lesson.ly](mailto:dev@lessonly.com).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/lessonly-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lessonly-test-user"></a>Vytvoření zkušebního uživatele Lesson.ly

Cílem této části je vytvoření uživatele v Lesson.ly nazývá Britta Simon. Lesson.ly podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k Lesson.ly, pokud ještě neexistuje.

> [!NOTE]
> Pokud potřebujete vytvořit uživatele s ručně, budete muset kontaktovat [tým podpory Lesson.ly](mailto:dev@lessonly.com).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Lesson.ly.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Lesson.ly, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Lesson.ly**.

    ![Konfigurovat jednotné přihlašování](./media/lessonly-tutorial/tutorial_lesson.ly_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Lesson.ly na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Lesson.ly.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/lessonly-tutorial/tutorial_general_01.png
[2]: ./media/lessonly-tutorial/tutorial_general_02.png
[3]: ./media/lessonly-tutorial/tutorial_general_03.png
[4]: ./media/lessonly-tutorial/tutorial_general_04.png

[100]: ./media/lessonly-tutorial/tutorial_general_100.png

[200]: ./media/lessonly-tutorial/tutorial_general_200.png
[201]: ./media/lessonly-tutorial/tutorial_general_201.png
[202]: ./media/lessonly-tutorial/tutorial_general_202.png
[203]: ./media/lessonly-tutorial/tutorial_general_203.png

