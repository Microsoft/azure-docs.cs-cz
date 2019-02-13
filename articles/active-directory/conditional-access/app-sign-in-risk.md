---
title: Rychlý start – zablokuje přístup, když se zjistí ohrožení relace s podmíněným přístupem Azure Active Directory | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak nakonfigurovat zásadu podmíněného přístupu Azure Active Directory (Azure AD) k blokování přihlášení podle rizika relace.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e950450a1ebe710a56ccfa0e8abefc7104602ec5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206170"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Rychlý start: Blokovat přístup, když se zjistí ohrožení relace s podmíněným přístupem Azure Active Directory  

Pokud chcete zachovat prostředí chráněný, můžete zablokovat podezřelé uživatele z přihlášení. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyzuje každé přihlášení a vypočítá pravděpodobnost, že pokus přihlášení nebyl prováděné legitimním vlastníkem uživatelského účtu. Pravděpodobnost (nízká, střední, vysoká) je uveden v podobě počítané hodnoty nazvané [úrovně rizika přihlášení](conditions.md#sign-in-risk). Tím, že nastavíte podmínku rizika přihlášení, můžete nakonfigurovat zásady podmíněného přístupu na úrovně riziko konkrétního přihlášení. 

Tento rychlý start ukazuje, jak nakonfigurovat [zásady podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) , která blokuje u přihlášení při zjistil úroveň nakonfigurované rizika přihlášení. 

![Vytvoření zásad](./media/app-sign-in-risk/1000.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="prerequisites"></a>Požadavky 

K dokončení scénáře v tomto kurzu budete potřebovat:

- **Přístup k Azure AD Premium P2 edice** – podmíněný přístup je schopnost Azure AD Premium P1, P2 edition musíte protože scénář v tomto kurzu rychlý start vyžaduje Identity Protection. 

- **Ochrana identity** – scénář v tomto kurzu rychlý start vyžaduje, aby byla povolena ochrana Identity. Pokud si nejste jisti, jak povolit Identity Protection, přečtěte si téma [povolení Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Prohlížeč Tor** – [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en) pomoct můžete zachovat vaše osobní údaje online. Identity Protection zjistí, že přihlášení v prohlížeči Tor jako **přihlášení z anonymních IP adres**, která má úroveň střední riziko. Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Testovací účet s názvem Alain Charon** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat cloudové uživatele](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Test přihlášení 

Cílem tohoto kroku je zajistit, že testovací účet můžete přístup k vašemu tenantovi pomocí prohlížeče Tor.

**K otestování vaše přihlášení:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako **Alain Charon**.

2. Odhlaste se. 


## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu 

Scénář v tomto rychlém startu se používá u přihlášení v prohlížeči Tor k vygenerování zjištěné **přihlášení z anonymních IP adres** riziková událost. Úroveň rizika tuto rizikovou událost je střední. Reakce na tuto rizikovou událost, nastavte podmínku rizika přihlašování na médium. V produkčním prostředí byste měli nastavit podmínku rizika přihlašování na vysoce nebo na střední a vysokou.     

Tato část ukazuje, jak vytvořit zásady podmíněného přístupu vyžaduje. V zásadách nastavte:

|Nastavení |Hodnota|
|---     | --- |
| Uživatelé a skupiny | Alain Charon  |
| Cloudové aplikace | Všechny cloudové aplikace |
| Riziko přihlášení | Střednědobé používání |
| Udělení | Blokovat přístup |
 

![Vytvoření zásad](./media/app-sign-in-risk/130.png)

 


**Ke konfiguraci zásady podmíněného přístupu:**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

2. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-sign-in-risk/02.png)

3. Na **Azure Active Directory** stránku, **zabezpečení** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/app-sign-in-risk/03.png)
 
4. Na **podmíněného přístupu** klikněte na stránku, na panelu nástrojů v horní části **přidat**.

    ![Název](./media/app-sign-in-risk/108.png)

5. Na **nový** stránku, **název** textové pole, typ **blokovat přístup k úrovni střední riziko**.

    ![Název](./media/app-sign-in-risk/104.png)

6. V **přiřazení** klikněte na tlačítko **uživatelů a skupin**.

    ![Uživatelé a skupiny](./media/app-sign-in-risk/06.png)

7. Na **uživatelů a skupin** stránky:

    ![Podmíněný přístup](./media/app-sign-in-risk/107.png)

    a. Klikněte na tlačítko **výběr uživatelů a skupin**a pak vyberte **uživatelů a skupin**.

    b. Klikněte na **Vybrat**.

    c. Na **vyberte** stránce **Alain Charon**a potom klikněte na tlačítko **vyberte**.

    d. Na **uživatelů a skupin** klikněte na **provádí**.

8. Klikněte na tlačítko **cloudové aplikace**.

    ![Cloudové aplikace](./media/app-sign-in-risk/08.png)

9. Na **cloudové aplikace** stránky:

    ![Podmíněný přístup](./media/app-sign-in-risk/109.png)

    a. Klikněte na tlačítko **všechny cloudové aplikace**.

    b. Klikněte na **Done** (Hotovo).

10. Klikněte na tlačítko **podmínky**. 

    ![Ovládací prvky přístupu](./media/app-sign-in-risk/19.png)

11. Na **podmínky** stránky:

    ![Úroveň rizika přihlášení](./media/app-sign-in-risk/21.png)

    a. Klikněte na tlačítko **rizika přihlašování**.
 
    b. Jako **konfigurovat**, klikněte na tlačítko **Ano**.

    c. Jako úroveň rizika přihlášení, vyberte **střední**.

    d. Klikněte na **Vybrat**.

    e. Na **podmínky** klikněte na **provádí**.



10. V **ovládací prvky přístupu** klikněte na tlačítko **udělení**.

    ![Ovládací prvky přístupu](./media/app-sign-in-risk/10.png)

11. Na **udělení** stránky:

    ![Podmíněný přístup](./media/app-sign-in-risk/105.png)

    a. Vyberte **blokovat přístup**.

    b. Klikněte na **Vybrat**.

12. V **povolit zásady** klikněte na tlačítko **na**.

    ![Povolit zásadu](./media/app-sign-in-risk/18.png)

13. Klikněte na možnost **Vytvořit**.


## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, pravděpodobně chcete zjistit, jestli fungují podle očekávání. Jako první krok, použijte podmíněný přístup **co když nástroj zásad** simulace u přihlášení z testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

Při spuštění **co když nástroj zásad** pro tento scénář **blokovat přístup k úrovni střední riziko** by měl být uvedený **zásady, které budou platit**. 

![Uživatel](./media/app-sign-in-risk/117.png)


**Vyhodnotit své zásady podmíněného přístupu:**

1. Na [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na stránce **co když**.  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. Klikněte na tlačítko **uživatele**vyberte **Alan Charon** na **uživatelé** stránce a potom klikněte na tlačítko **vyberte**.

    ![Uživatel](./media/app-sign-in-risk/116.png)

3. Jako **rizika přihlašování**vyberte **střední**.

    ![Uživatel](./media/app-sign-in-risk/119.png)


3. Klikněte na tlačítko **co když**.


## <a name="test-your-conditional-access-policy"></a>Otestování zásady podmíněného přístupu

V předchozí části jste zjistili, jak vyhodnotit simulované přihlášení. Kromě simulaci byste měli také otestovat své zásady podmíněného přístupu, abyste měli jistotu, že funguje podle očekávání. 

K otestování vašich zásad, zkuste pro přihlášení k vaší [webu Azure portal](https://portal.azure.com) jako **Alan Charon** pomocí prohlížeče sítě Tor. Váš pokus o přihlášení by měl být blokovány své zásady podmíněného přístupu.

![Ověřování pomocí služby Multi-Factor Authentication](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele, prohlížeč Tor a zásady podmíněného přístupu:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [odstranit uživatele z Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Pokud chcete odstranit zásady, vyberte zásady a klikněte na **odstranit** v panelu nástrojů Rychlý přístup.

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Pokyny k odebrání Tor prohlížeči najdete v tématu [odinstalace](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyžadovat podmínky použití přijmout](require-tou.md)
> [vyžadovat vícefaktorové ověřování pro konkrétní aplikace](app-based-mfa.md)

