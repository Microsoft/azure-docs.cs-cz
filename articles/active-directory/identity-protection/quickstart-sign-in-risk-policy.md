---
title: Rychlý Start – zablokuje přístup, když se zjistí riziko relace s Azure Active Directory Identity Protection | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak můžete nakonfigurovat zásady podmíněného přístupu pro přihlášení identity Azure Active Directory (Azure AD), které zablokují přihlášení na základě rizik relace.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5e5e26a27e13ba09ffcc97e0b2b0f1b37bc8bd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127700"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Rychlý start: Zablokovat přístup v případě zjištění rizika relace s Azure Active Directory Identity Protection  

Chcete-li zachovat ochranu prostředí, můžete chtít zablokovat podezřelé uživatele od přihlášení. Služba Azure Active Directory (Azure AD) Identity Protection analyzuje každé přihlášení a vypočítává pravděpodobnost, že oprávněný vlastník uživatelského účtu neudělal pokus o přihlášení. Pravděpodobnost (nízká, střední, vysoká) se označuje ve formě počítané hodnoty s názvem úroveň rizika přihlašování. Nastavením rizikové podmínky přihlášení můžete nakonfigurovat zásady podmíněného přístupu, které umožňují přihlašování reagovat na konkrétní úrovně rizika přihlašování. 

V tomto rychlém startu se dozvíte, jak nakonfigurovat zásady podmíněného přístupu pro přihlášení, které blokují přihlášení, když se zjistí úroveň rizika pro přihlášení na střední a vyšší. 

![Vytvořit zásadu](./media/quickstart-sign-in-risk-policy/1004.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky 

K dokončení scénáře v tomto kurzu budete potřebovat:

- **Přístup k edici Azure AD Premium P2** – Azure AD Identity Protection je funkce Azure AD Premium P2. 
- **Identity Protection** – scénář v tomto rychlém startu vyžaduje, aby byla povolená ochrana identity. Pokud si nejste jisti, jak povolit ochranu identity, přečtěte si téma [povolení Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Webový prohlížeč** – [prohlížeč pro mandát](https://www.torproject.org/projects/torbrowser.html.en) vám umožňuje zachovat ochranu osobních údajů online. Identity Protection detekuje přihlášení z prohlížeče mandátu jako **přihlášení z anonymních IP adres**, které mají střední úroveň rizika. Další informace najdete v tématu [Azure Active Directory detekci rizik](../reports-monitoring/concept-risk-events.md).  
- **Testovací účet s názvem Alain Charon** – Pokud si nejste jisti, jak vytvořit testovací účet, přečtěte si téma [Přidání nového uživatele](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Otestování přihlášení 

Cílem tohoto kroku je zajistit, že váš testovací účet bude mít přístup k vašemu tenantovi pomocí prohlížeče systému.

**Testování přihlášení:**

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com) jako **Alain Charon**.
2. Odhlaste se. 

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu 

Scénář v tomto rychlém startu používá k vygenerování zjištěných přihlášení ze zjišťování rizik anonymních **IP adres** přihlášení z prohlížeče pro mandát. Úroveň rizika tohoto zjišťování rizik je střední. Pokud chcete reagovat na toto zjištění rizika, nastavte rizikové podmínky přihlášení na hodnotu Střední. 

V této části se dozvíte, jak vytvořit požadované zásady podmíněného přístupu pro rizikové přihlašování. V zásadách nastavte:

|Nastavení |Value|
|---     | --- |
| Uživatelé  | Alain Charon  |
| Podmínky | Riziko přihlášení, střední a vyšší |
| Ovládací prvky | Blokovat přístup |

![Vytvořit zásadu](./media/quickstart-sign-in-risk-policy/201.png)

**Konfigurace zásad podmíněného přístupu:**

1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com) jako globální správce.
2. Přejít na [stránku Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Na stránce **Azure AD Identity Protection** v části **Konfigurace** klikněte na možnost **zásady rizik přihlašování**.
4. Na stránce Zásady klikněte v části **přiřazení** na **Uživatelé**.
5. Na stránce **Uživatelé** klikněte na **Vybrat uživatele**.
6. Na stránce **Vybrat uživatele** vyberte **Alain Charon**a pak klikněte na **Vybrat**.
7. Na stránce **Uživatelé** klikněte na **Hotovo**. 
8. Na stránce Zásady klikněte v části **přiřazení** na možnost **podmínky**.
9. Na stránce **podmínky** klikněte na možnost **rizika přihlášení**.
10. Na stránce **riziko přihlášení** vyberte **střední a vyšší**a pak klikněte na **Vybrat**. 
11. Na stránce **podmínky** klikněte na **Hotovo**.
12. Na stránce zásady v části **ovládací prvky** klikněte na **přístup**.
13. Na stránce **přístup** klikněte na možnost **Povolení přístupu**, vyberte možnost **požadovat vícefaktorové ověřování**a pak klikněte na tlačítko **Vybrat**.
14. Na stránce Zásady klikněte na **Uložit**.  

## <a name="test-your-conditional-access-policy"></a>Testování zásad podmíněného přístupu

Pokud chcete zásady otestovat, zkuste se k vašemu [Azure Portal](https://portal.azure.com) přihlašovat jako **Jakub Charon** pomocí prohlížeče pro prostředí. Váš pokus o přihlášení by měl blokovat vaše zásada podmíněného přístupu.

![Ověřování pomocí služby Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele, prohlížeč pro mandát a zakažte zásady podmíněného přístupu pro přihlášení:

- Pokud si nejste jisti, jak odstranit uživatele služby Azure AD, přečtěte si téma [jak přidat nebo odstranit](../fundamentals/add-users-azure-active-directory.md#delete-a-user)uživatele.
- Pokyny k odebrání prohlížeče pro mandát najdete v tématu [](https://tb-manual.torproject.org/uninstalling/)věnovaném odinstalaci.
