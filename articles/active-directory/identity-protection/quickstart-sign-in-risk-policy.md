---
title: Rychlý start – zablokuje přístup, když se zjistí ohrožení relace s Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: V tomto rychlém startu zjistíte, jak nakonfigurovat zásadu podmíněného přístupu Azure Active Directory (Azure AD) Identity Protection rizika přihlašování k blokování přihlášení podle rizika relace.
services: active-directory
keywords: podmíněný přístup aplikací, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu a podmíněný přístup s Azure AD identity protection
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 6879822e8451e6170bf17fff4ab66b672f711a93
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632383"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Rychlý start: Blokování přístupu, když se zjistí ohrožení relace s Azure Active Directory Identity Protection  

Pokud chcete zachovat prostředí chráněný, můžete zablokovat podezřelé uživatele z přihlášení. Azure Active Directory (Azure AD) Identity Protection analyzuje každé přihlášení a vypočítá pravděpodobnost, že pokus přihlášení nebyl prováděné legitimním vlastníkem uživatelského účtu. Pravděpodobnost (nízká, střední, vysoká) je uveden v podobě počítané hodnoty nazvané úroveň rizika přihlášení. Tím, že nastavíte podmínku rizika přihlášení, můžete nakonfigurovat zásady podmíněného přístupu rizika přihlašování reagovat na určité riziko přihlášení úrovně. 

Tento rychlý start ukazuje, jak nakonfigurovat zásady podmíněného přístupu rizika přihlašování blokující u přihlášení při střední a vyšší riziko přihlášení byl zjištěn úroveň. 

![Vytvoření zásad](./media/quickstart-sign-in-risk-policy/1004.png)


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.



## <a name="prerequisites"></a>Požadavky 

K dokončení scénáře, který v tomto kurzu, budete potřebovat:

- **Přístup k Azure AD Premium P2 edice** – Azure AD Identity Protection je funkce služby Azure AD Premium P2. 

- **Ochrana identity** – scénář v tomto kurzu rychlý start vyžaduje, aby byla povolena ochrana Identity. Pokud si nejste jisti, jak povolit Identity Protection, přečtěte si téma [povolení Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Prohlížeč Tor** – [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en) pomoct můžete zachovat vaše osobní údaje online. Identity Protection zjistí, že přihlášení v prohlížeči Tor jako **přihlášení z anonymních IP adres**, která má úroveň střední riziko. Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Testovací účet s názvem Alain Charon** – Pokud si nejste jisti, jak vytvořit testovací účet, najdete v článku [přidat nového uživatele](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Test přihlášení 

Cílem tohoto kroku je zajistit, že testovací účet můžete přístup k vašemu tenantovi pomocí prohlížeče Tor.

**K otestování vaše přihlášení:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako **Alain Charon**.

2. Odhlaste se. 


## <a name="create-your-conditional-access-policy"></a>Vytvořte zásadu podmíněného přístupu 

Scénář v tomto rychlém startu se používá u přihlášení v prohlížeči Tor k vygenerování zjištěné **přihlášení z anonymních IP adres** riziková událost. Úroveň rizika tuto rizikovou událost je střední. Reakce na tuto rizikovou událost, nastavte podmínku rizika přihlašování na médium. 

Tato část ukazuje, jak vytvořit zásady podmíněného přístupu vyžaduje rizika přihlášení. V zásadách nastavte:

|Nastavení |Hodnota|
|---     | --- |
| Uživatelé  | Alain Charon  |
| Podmínky | Riziko přihlášení, střední a vyšší |
| Ovládací prvky | Blokovat přístup |
 

![Vytvoření zásad](./media/quickstart-sign-in-risk-policy/201.png)

 


**Ke konfiguraci zásady podmíněného přístupu:**

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce.

2. Přejděte [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. Na **Azure AD Identity Protection** stránku, **konfigurovat** klikněte na tlačítko **zásady rizik přihlašování**.
 
4. Na stránce zásad v **přiřazení** klikněte na tlačítko **uživatelé**.

5. Na **uživatelé** klikněte na **vybraným uživatelům**.

6. Na **vybraným uživatelům** stránce **Alain Charon**a potom klikněte na tlačítko **vyberte**.

7. Na **uživatelé** klikněte na **provádí**. 

8. Na stránce zásad v **přiřazení** klikněte na tlačítko **podmínky**.

9. Na **podmínky** klikněte na **rizika přihlašování**.

10. Na **rizika přihlašování** stránce **střední a vyšší**a potom klikněte na tlačítko **vyberte**. 

11. Na **podmínky** klikněte na **provádí**.

12. Na stránce zásad v **ovládací prvky** klikněte na tlačítko **přístup**.

13. Na **přístup** klikněte na **povolit přístup**vyberte **vyžadovat vícefaktorové ověřování**a potom klikněte na tlačítko **vyberte**.

14. Klikněte na stránce zásad **Uložit**.  


## <a name="test-your-conditional-access-policy"></a>Otestovat své zásady podmíněného přístupu

K otestování vašich zásad, zkuste pro přihlášení k vaší [webu Azure portal](https://portal.azure.com) jako **Alan Charon** pomocí prohlížeče sítě Tor. Váš pokus o přihlášení by měl být blokovány své zásady podmíněného přístupu.

![Ověřování pomocí služby Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete, odstraňte testovacího uživatele Tor prohlížeče a zakázat zásady podmíněného přístupu rizika přihlášení:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [postup přidání nebo odstranění uživatelů](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Pokyny k odebrání Tor prohlížeči najdete v tématu [odinstalace](https://tb-manual.torproject.org/en-US/uninstalling.html).


