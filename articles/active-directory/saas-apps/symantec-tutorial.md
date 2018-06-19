---
title: 'Kurz: Azure Active Directory integrace s Symantec webové zabezpečení služby (WSS) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Symantec webové zabezpečení služby (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 07a6211c624f85e7f75cc3a0b0247cd085e7bf96
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903211"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Azure Active Directory integrace s Symantec webové zabezpečení služby (WSS)

V tomto kurzu se dozvíte, jak integrovat účtu Symantec webové zabezpečení služby (WSS) s vaším účtem Azure Active Directory (Azure AD), aby mohli WSS ověření koncový uživatel zřízenou ve službě Azure AD pomocí ověřování SAML a vynucovat pravidla zásadou na úrovni uživatele nebo skupinu.

Integrace Symantec webové zabezpečení služby (WSS) s Azure AD poskytuje následující výhody:

- Spravujte všechny uživatele a skupiny používané účtu WSS z portálu Azure AD. 

- Povolit koncoví uživatelé mohli sami ověřit v WSS pomocí svých přihlašovacích údajů Azure AD.

- Povolte vynucení uživatele a skupiny zásadou na úrovni pravidla definovaná v účtu WSS.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Symantec webové zabezpečení služby (WSS), potřebujete následující položky:

- Předplatné služby Azure AD
- Účet služby pro zabezpečení webové Symantec (WSS)

> [!NOTE]
> Chcete-li otestovat kroky v tomto kurzu, nedoporučujeme pomocí WSS účtu, který je aktuálně používá pro produkční účely.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte účtu WSS, který je aktuálně používá pro produkční účely pro tento test Pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu nakonfigurujete Azure AD povolit jednotné přihlašování k WSS pomocí přihlašovacích údajů koncového uživatele, které jsou definované v účtu Azure AD.
Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání aplikace Symantec webové zabezpečení služby (WSS) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Přidání Symantec webové zabezpečení služby (WSS) z Galerie
Při konfiguraci integrace Symantec webové zabezpečení služby (WSS) do služby Azure AD, potřebujete přidat Symantec webové zabezpečení služby (WSS) z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Symantec webové zabezpečení služby (WSS) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Symantec webové zabezpečení služby (WSS)**, vyberte **Symantec webové zabezpečení služby (WSS)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Symantec webové zabezpečení služby (WSS) v seznamu výsledků](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Symantec webové zabezpečení služby (WSS) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Symantec webové zabezpečení služby (WSS).

V Symantec webové zabezpečení služby (WSS) přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Symantec webové zabezpečení služby (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  – Pokud chcete mít protějšek Britta Simon v zabezpečení webové Symantec služby (WSS) propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Symantec webové zabezpečení služby (WSS).

**Pokud chcete konfigurovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), proveďte následující kroky:**

1. Na portálu Azure na **Symantec webové zabezpečení služby (WSS)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Na **Symantec webové zabezpečení služby (WSS) domény a adresy URL** část, proveďte následující kroky:

    ![Symantec webové zabezpečení služby (WSS) domény a adresy URL jednotné přihlašování informace](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Obraťte se [tým podpory Symantec webové zabezpečení služby (WSS) klienta](https://www.symantec.com/contact-us) Pokud hodnoty **identifikátor** a **adresa URL odpovědi** nefungují z nějakého důvodu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/symantec-tutorial/tutorial_general_400.png)
    
6. Na straně Symantec webové zabezpečení služby (WSS) nakonfigurovat jednotné přihlašování, naleznete v online dokumentaci WSS. Stažené **soubor XML s metadaty** bude nutné importovat do portálu WSS soubor. Obraťte se [tým podpory Symantec webové zabezpečení služby (WSS)](https://www.symantec.com/contact-us) Pokud potřebujete pomoc s konfigurací na portálu WSS.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/symantec-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/symantec-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/symantec-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/symantec-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Vytvoření zkušebního uživatele Symantec webové zabezpečení služby (WSS)

V této části vytvoříte uživatele volat Britta Simon v Symantec webové zabezpečení služby (WSS). Uživatelské jméno odpovídající end je možné ručně vytvořit na portálu služby WSS nebo můžete počkat, uživatele nebo skupiny zřízené v Azure AD synchronizovány se službou portálu WSS po několika minutách (~ 15 minut). Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. Veřejnou IP adresu počítače koncového uživatele, který se použije k procházet weby se také muset zřídit na portálu společnosti Symantec webové zabezpečení služby (WSS).

> [!NOTE]
> Prosím [kliknutím sem](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) získat váš počítač je veřejná IP adresa.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k Symantec webové zabezpečení služby (WSS).

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Symantec webové zabezpečení služby (WSS), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Symantec webové zabezpečení služby (WSS)**.

    ![V seznamu aplikací na odkaz Symantec webové zabezpečení služby (WSS)](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části budete testovat funkce přihlašování nyní, když jste nakonfigurovali WSS účet pro použití služby Azure AD pro ověřování SAML.

Po konfiguraci webového prohlížeče proxy provoz na WSS, když otevřete ve webovém prohlížeči a zkuste a přejděte web, pak budete přesměrováni na stránku Azure přihlášení. Zadejte přihlašovací údaje testovacího koncového uživatele, která byla zajištěna ve službě Azure AD (BrittaSimon) a přidružené heslo. Po ověření, budete moct přejít na web, který jste si zvolili. Můžete vytvořit pravidlo zásad na straně WSS zablokujete BrittaSimon procházení k určitému webu a zobrazí stránku bloku WSS při pokusu o Procházet k této lokalitě jako uživatel BrittaSimon.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png

