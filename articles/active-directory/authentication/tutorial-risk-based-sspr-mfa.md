---
title: Vícefaktorové ověřování na základě rizikové události a samoobslužné resetování hesla pomocí služby Azure Identity Protection
description: V tomto kurzu povolíte integrace vícefaktorového ověřování (MFA – Multi-Factor Authentication) a samoobslužného resetování hesla služby Azure Identity Protection, abyste snížili rizikové chování.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: fea87a711bf43ea73d3e09efeb42f5133d8f4e9f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434916"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Kurz: Pomocí aktivační události služby Multi-Factor Authentication a změn hesel rizikových událostí

V tomto kurzu zapnete funkce služby Azure AD Identity Protection a funkci služby Azure AD Premium P2, která je víc než jenom nástroj k monitorování a vytváření sestav. Identity v organizaci můžete chránit nakonfigurováním zásad na základě rizikové událostí, které automaticky reagují na rizikové chování. Tyto zásady mohou buď uživatele automaticky zablokovat, nebo mohou zajistit nápravu v podobě vynucení změny hesla či vícefaktorového ověřování.

Zásady Azure AD Identity Protection je možné společně se stávajícími zásadami podmíněného přístupu použít jako další úroveň ochrany. U vašich uživatelů nemusí nikdy k aktivaci jedné z těchto zásad v závislosti na rizikovém chování dojít, ale jako správce budete vědět, že jsou pro všechny případy chránění.

Rizikovou událost mohou aktivovat například následující akce:

* Uživatelé s uniklými přihlašovacími údaji
* Přihlášení z anonymních IP adres
* Nemožná cesta do netypických míst
* Přihlášení z nakažených zařízení
* Přihlášení z IP adres s podezřelou aktivitou
* Přihlášení z neznámých míst

Další informace o službě Azure AD Identity Protection najdete v článku [vysvětlujícím tuto službu](../active-directory-identityprotection.md).

> [!div class="checklist"]
> * Zapnutí registrace k Azure MFA
> * Zapnutí změn hesla na základě rizikové události
> * Zapnutí vícefaktorového ověřování na základě rizikové události

## <a name="prerequisites"></a>Požadavky

* Přístup k funkčnímu tenantovi Azure AD, který má přiřazenou alespoň zkušební licenci Azure AD Premium P2
* Účet s oprávněními globálního správce v tenantovi Azure AD
* Dokončení předchozích kurzů na téma samoobslužné resetování hesla (SSPR) a vícefaktorové ověřování (MFA)

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Zapnutí zásad na základě rizikové události pro samoobslužné resetování hesla a vícefaktorové ověřování

Povolení zásad na základě rizikové události je jednoduchý proces. Následující postup vás provede ukázkovou konfigurací.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Zapnutí registrace uživatelů k vícefaktorovému ověřování

Azure AD Identity Protection obsahuje výchozí zásadu, která vám může pomoct s registrací uživatelů k vícefaktorovému ověřování a snadněji identifikovat aktuální stav registrace. Povolením této zásady nespustíte výzvy k provedení vícefaktorového ověřování, ale požádáte uživatele, aby se k němu předběžně zaregistrovali.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Klikněte na **Všechny služby** a přejděte na **Azure AD Identity Protection**.
1. Klikněte na **Registrace MFA**.
1. **Zapněte** vynucení zásady.
   1. Nastavením této zásady se začne od všech uživatelů vyžadovat registrace metod vícefaktorového ověřování, aby se připravili na jeho používání.
1. Klikněte na **Uložit**.

   ![Vyžádání registrace uživatelů k vícefaktorovému ověřování při přihlašování pomocí Azure AD Identity Protection](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Zapnutí změn hesla na základě rizikové události

Microsoft spolupracuje při vyhledávání dvojic uživatelských jmen a hesel s výzkumnými pracovníky, orgány zajišťujícími vymáhání zákona, různými týmy zabezpečení v Microsoftu a dalšími důvěryhodnými zdroji. Když některá dvojice odpovídá účtu ve vašem prostředí, může se následující zásadou aktivovat změna hesla na základě rizikové události.

1. Klikněte na Zásady rizik uživatelů.
1. V části **Podmínky** vyberte **Riziko uživatele** a potom vyberte **Střední a vyšší**.
1. Klikněte na Vybrat a potom na Hotovo.
1. V části **Přístup** zvolte **Povolit přístup** a pak vyberte **Požadovat změnu hesla**.
1. Klikněte na Vybrat.
1. **Zapněte** vynucení zásady.
1. Klikněte na **Uložit**.

### <a name="enable-risk-based-multi-factor-authentication"></a>Zapnutí vícefaktorového ověřování na základě rizikové události

Většina uživatelů se chová běžným způsobem, který je možné sledovat. Když se mimo tento běžný způsob vychýlí, může být nebezpečné jim povolit se normálně přihlásit. Takovéto uživatele můžete zablokovat nebo je můžete požádat o provedení vícefaktorového ověření, aby prokázali, že jsou to skutečně oni. Vyžádání vícefaktorového ověření při zjištění rizikového přihlášení zapnete povolením následující zásady.

1. Klikněte na Zásady rizik přihlašování
1. V části **Podmínky** vyberte **Riziko uživatele** a potom vyberte **Střední a vyšší**.
1. Klikněte na Vybrat a potom na Hotovo.
1. V části **Přístup** zvolte **Povolit přístup** a pak vyberte **Vyžadovat vícefaktorové ověřování**.
1. Klikněte na Vybrat.
1. **Zapněte** vynucení zásady.
1. Klikněte na **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste skončili s testováním a už zásady na základě rizikové události mít povolené nechcete, vraťte se ke každé zásadě, kterou chcete zakázat, a nastavte **Vynucení zásady** na **Vypnuto**.