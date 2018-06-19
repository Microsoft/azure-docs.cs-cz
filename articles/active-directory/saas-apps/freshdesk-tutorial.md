---
title: 'Kurz: Azure Active Directory integrace s FreshDesk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e92cb4257b5b1e2d39b07fa93088d6364fdc4c5d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901794"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Azure Active Directory integrace s FreshDesk

V tomto kurzu zjistěte, jak integrovat FreshDesk s Azure Active Directory (Azure AD).

Integrace FreshDesk s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FreshDesk
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FreshDesk (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FreshDesk, potřebujete následující položky:

- Předplatné služby Azure AD
- FreshDesk jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FreshDesk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání FreshDesk z Galerie
Při konfiguraci integrace FreshDesk do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS FreshDesk z galerie.

**Pokud chcete přidat FreshDesk z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **FreshDesk**.

    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_search.png)

5. Na panelu výsledků vyberte **FreshDesk**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s FreshDesk podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v FreshDesk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v FreshDesk musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v FreshDesk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FreshDesk, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele FreshDesk](#creating-a-freshdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v FreshDesk propojeném s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci FreshDesk.

**Ke konfiguraci Azure AD jednotné přihlašování s FreshDesk, proveďte následující kroky:**

1. Na portálu Azure Management portal na **FreshDesk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogové okno, jako **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na **FreshDesk domény a adresy URL** části, zadejte **přihlašovací adresa URL** jako: `https://<tenant-name>.freshdesk.com` nebo jakoukoli jinou hodnotu Freshdesk navrhl.

    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Upozorňujeme, že se nejedná skutečné hodnoty. Budete muset aktualizovat hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory FreshDesk klienta](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) získat tuto hodnotu.  

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikát** a potom uložte certifikát v počítači.

    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_general_400.png)

6. Na **FreshDesk konfigurace** klikněte na tlačítko **konfigurace FreshDesk** otevřete konfigurovat přihlašování okno. Zkopírujte SAML jeden přihlašování adresa URL služby a adresa URL Sign-Out z **Stručná referenční příručka** části.

    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Freshdesk.

8. V nabídce v horní části, klikněte na tlačítko **správce**.
   
   ![Správce](./media/freshdesk-tutorial/IC776768.png "správce")

9. V **obecné nastavení** , klikněte na **zabezpečení**.
   
   ![Zabezpečení](./media/freshdesk-tutorial/IC776769.png "zabezpečení")

10. V **zabezpečení** část, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/freshdesk-tutorial/IC776770.png "jednotné přihlašování")
   
    a. Pro **jednotné přihlašování na (SSO)**, vyberte **na**.

    b. Vyberte **jednotné přihlašování SAML**.

    c. Typ **SAML jeden přihlašování adresa URL služby** jste zkopírovali z portálu Azure do **SAML přihlašovací adresa URL** textové pole.

    d. Typ **Sign-Out URL** jste zkopírovali z portálu Azure do **adresy URL odhlašovací** textové pole.

    e. Kopírování **kryptografický otisk** hodnotu z certifikát stažený z portálu Azure a vložte ji do **otisků certifikátu zabezpečení** textové pole.  
 
    >[!TIP]
    >Další podrobnosti najdete v tématu [jak načíst hodnoty kryptografického otisku certifikátu](http://youtu.be/YKQF266SAxI). 
    
    f. Klikněte na **Uložit**.


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-freshdesk-test-user"></a>Vytvoření zkušebního uživatele FreshDesk

Pokud chcete povolit uživatelům Azure AD přihlášení do FreshDesk, musí být zřízená do FreshDesk.  
V případě FreshDesk zřizování je ruční úloha.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Freshdesk** klienta.
2. V nabídce v horní části, klikněte na tlačítko **správce**.
   
   ![Správce](./media/freshdesk-tutorial/IC776772.png "správce")

3. V **obecné nastavení** , klikněte na **agenti**.
   
   ![Agenti](./media/freshdesk-tutorial/IC776773.png "agentů")

4. Klikněte na tlačítko **nového agenta**.
   
    ![Nový Agent](./media/freshdesk-tutorial/IC776774.png "nového agenta.")

5. V dialogovém okně informace o agentovi proveďte následující kroky:
   
   ![Informace o agentovi](./media/freshdesk-tutorial/IC776775.png "informace o agentovi")
   
   a. V **úplný název** textovému poli, zadejte název účtu Azure AD, které chcete zřídit.

   b. V **e-mailu** textovému poli, typ Azure AD e-mailovou adresu chcete zřídit účet Azure AD.

   c. V **název** textovému poli, zadejte název chcete zřídit účet Azure AD.

   d. Vyberte **agenti role**a potom klikněte na **přiřadit**.
       
   e. Klikněte na **Uložit**.     
   
    >[!NOTE]
    >Držitel účtu Azure AD dostane e-mail, který obsahuje odkaz pro potvrzení účtu předtím, než se aktivuje. 
    > 
    
    >[!NOTE]
    >Můžete použít všechny ostatní Freshdesk uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Freshdesk zřídit AAD uživatelské účty. k FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k poli.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon FreshDesk, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **FreshDesk**.

    ![Konfigurovat jednotné přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici FreshDesk na přístupovém panelu, měli byste obdržet přihlašovací stránku k získání přihlášení k aplikaci FreshDesk.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png

