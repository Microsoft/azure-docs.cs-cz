---
title: 'Kurz: Azure Active Directory integrace s NetDocuments | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 0f359c0cb26f6d6903b890da7d11f64c92a1f51a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899330"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Kurz: Azure Active Directory integrace s NetDocuments

V tomto kurzu zjistěte, jak integrovat NetDocuments s Azure Active Directory (Azure AD).

Integrace NetDocuments s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k NetDocuments
- Můžete povolit uživatelům, aby automaticky získat přihlášení k NetDocuments (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s NetDocuments, potřebujete následující položky:

- Předplatné služby Azure AD
- NetDocuments jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání NetDocuments z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-netdocuments-from-the-gallery"></a>Přidání NetDocuments z Galerie
Při konfiguraci integrace NetDocuments do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS NetDocuments z galerie.

**Pokud chcete přidat NetDocuments z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **NetDocuments**.

    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

5. Na panelu výsledků vyberte **NetDocuments**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s NetDocuments podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v NetDocuments je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v NetDocuments musí navázat.

V NetDocuments, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s NetDocuments, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele NetDocuments](#creating-a-netdocuments-test-user)**  – Pokud chcete mít protějšek Britta Simon v NetDocuments propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci NetDocuments.

**Ke konfiguraci Azure AD jednotné přihlašování s NetDocuments, proveďte následující kroky:**

1. Na portálu Azure na **NetDocuments** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

3. Na **NetDocuments domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL přihlašování a adresa URL odpovědi. Obraťte se na [tým podpory NetDocuments](https://support.netdocuments.com/hc/) k získání těchto hodnot.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/netdocuments-tutorial/tutorial_general_400.png)

6. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti NetDocuments.

7. Přejděte na **správce**.

8. Klikněte na tlačítko **přidat a odebrat uživatele a skupiny**.
   
    ![Úložiště](./media/netdocuments-tutorial/ic795047.png "úložiště")

9. Klikněte na tlačítko **konfigurovat pokročilé možnosti ověřování**.
    
    ![Nakonfigurujte rozšířené možnosti ověřování](./media/netdocuments-tutorial/ic795048.png "konfigurovat pokročilé možnosti ověřování")

10. Na **federované Identity** dialogové okno, proveďte následující kroky:
   
    ![Federované Identitty](./media/netdocuments-tutorial/ic795049.png "federovaný Identitty")
   
    a. Jako **federované identity serveru typ**, vyberte **Active Directory Federation Services**.
   
    b. Klikněte na tlačítko **zvolte soubor**, nahrát soubor stažený metadata, která jste si stáhli z portálu Azure.
   
    c. Klikněte na **OK**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-netdocuments-test-user"></a>Vytvoření zkušebního uživatele NetDocuments

Pokud chcete povolit uživatelům Azure AD přihlášení k NetDocuments, musí být zřízená do NetDocuments.  
V případě NetDocuments zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. SING na vaše **NetDocuments** společnosti lokality jako správce.

2. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Správce](./media/netdocuments-tutorial/ic795051.png "správce")

3. Klikněte na tlačítko **přidat a odebrat uživatele a skupiny**.
   
    ![Úložiště](./media/netdocuments-tutorial/ic795047.png "úložiště")

4. V **e-mailovou adresu** textovému poli, zadejte e-mailovou adresu platný účet služby Azure Active Directory určené ke zřízení a potom klikněte na **přidat uživatele**.
   
    ![E-mailová adresa](./media/netdocuments-tutorial/ic795053.png "e-mailová adresa")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory dostane e-mail, který obsahuje odkaz pro potvrzení účtu před stane aktivní. Můžete použít všechny ostatní NetDocuments uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované NetDocuments zřídit služby Azure Active Directory uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu NetDocuments.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon NetDocuments, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **NetDocuments**.

    ![Konfigurovat jednotné přihlašování](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici NetDocuments na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci NetDocuments.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

