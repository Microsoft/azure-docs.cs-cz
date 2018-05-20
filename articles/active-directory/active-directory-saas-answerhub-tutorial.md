---
title: 'Kurz: Azure Active Directory integrace s AnswerHub | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: c4d63b5a51a6cf6626c42b0ef39587f1e760d238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Kurz: Azure Active Directory integrace s AnswerHub

V tomto kurzu zjistěte, jak integrovat AnswerHub s Azure Active Directory (Azure AD).

Integrace AnswerHub s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AnswerHub
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AnswerHub (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AnswerHub, potřebujete následující položky:

- Předplatné služby Azure AD
- AnswerHub jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AnswerHub z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-answerhub-from-the-gallery"></a>Přidání AnswerHub z Galerie
Při konfiguraci integrace AnswerHub do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS AnswerHub z galerie.

**Pokud chcete přidat AnswerHub z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AnswerHub**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. Na panelu výsledků vyberte **AnswerHub**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s AnswerHub podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v AnswerHub je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v AnswerHub musí navázat.

V AnswerHub, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AnswerHub, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele AnswerHub](#creating-an-answerhub-test-user)**  – Pokud chcete mít protějšek Britta Simon v AnswerHub propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci AnswerHub.

**Ke konfiguraci Azure AD jednotné přihlašování s AnswerHub, proveďte následující kroky:**

1. Na portálu Azure na **AnswerHub** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Na **AnswerHub domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company>.answerhub.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory AnswerHub klienta](mailto:success@answerhub.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. Na **AnswerHub konfigurace** klikněte na tlačítko **konfigurace AnswerHub** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti AnswerHub.
   
    >[!NOTE]
    >Pokud potřebujete pomoc s konfigurací AnswerHub, obraťte se na [tým podpory pro AnswerHub](mailto:success@answerhub.com.).
   
8. Přejděte na **správy**.

9. Klikněte **uživatelů a skupin** kartě.

10. V navigačním podokně na levé straně v **sociálních nastavení** klikněte na tlačítko **SAML instalace**.

11. Klikněte na tlačítko **IDP konfigurace** kartě.

12. Na **IDP konfigurace** kartu, proveďte následující kroky:

     ![Instalační program SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "instalace SAML")  
  
     a. V **IDP přihlašovací adresa URL** textovému poli, vložte **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
  
     b. V **adresy URL odhlašovací IDP** textovému poli, vložte **Sign-Out URL** hodnotu, která jste zkopírovali z portálu Azure.
     
     c. V **formát identifikátor názvu IDP** textovému poli, zadejte uživatele identifikátor stejná hodnota jako vybrané v portálu Azure v **uživatelské atributy** části.
  
     d. Klikněte na tlačítko **klíčů a certifikátů**.

13. Na kartě klíčů a certifikátů proveďte následující kroky:
    
     ![Klíčů a certifikátů](./media/active-directory-saas-answerhub-tutorial/ic785173.png "klíčů a certifikátů")  
 
     a. Vaše kódování base-64 kódovaného certifikátu, který jste si stáhli z portálu Azure v poznámkovém bloku otevřete kopírovat obsah ho do schránky a vložte jej do **IDP veřejný klíč (x 509 formátu)** textové pole.
  
     b. Klikněte na **Uložit**.

14. Na **IDP konfigurace** , klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-answerhub-test-user"></a>Vytváření testovacího uživatele AnswerHub

Pokud chcete povolit uživatelům Azure AD přihlášení k AnswerHub, musí být zřízená do AnswerHub.  
V případě AnswerHub zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **AnswerHub** společnosti lokality jako správce.

2. Přejděte na **správy**.

3. Klikněte **uživatelé a skupiny** kartě.

4. V navigačním podokně na levé straně v **spravovat uživatele** klikněte na tlačítko **vytvoření nebo import uživatelů**.
   
   ![Uživatelé a skupiny](./media/active-directory-saas-answerhub-tutorial/ic785175.png "uživatelé a skupiny")

5. Typ **e-mailová adresa**, **uživatelské jméno** a **heslo** platného účtu Azure Active Directory, kterou chcete zřídit do související textová pole a pak klikněte na **Uložit**.

>[!NOTE]
>Můžete použít všechny ostatní AnswerHub uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované AnswerHub zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu AnswerHub.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon AnswerHub, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **AnswerHub**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici AnswerHub na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci AnswerHub.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png

