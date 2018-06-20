---
title: 'Kurz: Azure Active Directory integrace s hostované grafitová | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hostované grafitová.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: dc9d5964a71d1cb33e6d5ddd974160f59c8d8c06
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226387"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Kurz: Azure Active Directory integrace s hostované grafitová

V tomto kurzu zjistěte, jak integrovat grafitová hostované v Azure Active Directory (Azure AD).

Integrace hostované grafitová s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k hostované grafitová
- Můžete povolit uživatelům, aby automaticky získat přihlášení k hostované grafitová (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s hostované grafitová, potřebujete následující položky:

- Předplatné služby Azure AD
- Hostované grafitová jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání hostované grafitová z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hosted-graphite-from-the-gallery"></a>Přidání hostované grafitová z Galerie
Při konfiguraci integrace hostované grafitu do služby Azure AD, potřebujete přidat hostované grafitová z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat hostované grafitová z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **hostované grafitová**.

    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

5. Na panelu výsledků vyberte **hostované grafitová**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s hostované grafitová podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v hostované grafitová je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v hostované grafitová musí navázat.

V hostované grafitová přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s hostované grafitová, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele hostované grafitová](#creating-a-hosted-graphite-test-user)**  – Pokud chcete mít protějšek Britta Simon v hostované grafitová propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci hostované grafitová.

**Ke konfiguraci Azure AD jednotné přihlašování s hostované grafitová, proveďte následující kroky:**

1. Na portálu Azure na **hostované grafitová** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

3. Na **hostované grafitová domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.hostedgraphite.com/metadata/<user id>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.hostedgraphite.com/complete/saml/<user id>`

4. Na **hostované grafitová domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Klikněte na **zobrazit upřesňující nastavení adresy URL** možnost

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Upozorňujeme, že tyto nejsou skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Chcete-li získat tyto hodnoty, můžete přejít na přístup -> Nastavení SAML na straně aplikace nebo kontaktujte [tým podpory hostované grafitová](mailto:help@hostedgraphite.com).
    >
 
5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_general_400.png)

7. Na **hostované konfigurace grafitová** klikněte na tlačítko **konfigurace hostované grafitová** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

8. Přihlášení ke klientovi hostované grafitová jako správce.

9. Přejděte na **stránce instalace SAML** na bočním panelu (**přístup -> Nastavení SAML**).
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

10. Potvrďte tyto adresy URL neodpovídá konfiguraci provést na **hostované grafitová domény a adresy URL** části portálu Azure.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

11. V **Entity nebo ID vystavitele** a **adresu URL pro přihlášení SSO** textových polí, vložte hodnotu **SAML Entity ID** a **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure. 
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

12. Vyberte "**jen pro čtení**" jako **výchozí Role uživatele**.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

13. Otevření kódovaného certifikátu kódování base-64 v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textové pole.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

14. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/hostedgraphite-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Vytvoření zkušebního uživatele hostované grafitová

Cílem této části je vytvoření uživatele volal Britta Simon v hostované grafitová. Hostované grafitová podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k hostované grafitová, pokud ještě neexistuje.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat tým podpory hostované grafitová prostřednictvím <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k hostované grafitová.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon hostované grafitová, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **hostované grafitová**.

    ![Konfigurovat jednotné přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici grafitová hostované na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci hostované grafitová.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/hostedgraphite-tutorial/tutorial_general_203.png

