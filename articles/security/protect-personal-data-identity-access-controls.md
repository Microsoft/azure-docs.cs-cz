---
title: "Ochrana osobních údajů s ovládacími prvky Azure identit a přístupu | Microsoft Docs"
description: "Azure identit a přístupu řídí pomáhají chránit osobní údaje a může být užitečné, které jsou v souladu s obecné Data Protection nařízení (GDPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: fd3beb57cda4993f922fb935263b0e962b38da6a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory a služby Multi-Factor Authentication: ochrana osobních dat s ovládacími prvky identit a přístupu

Tento článek obsahuje informace a postupy, které můžete použít k ochraně osobní data pomocí Azure Active Directory a službou Multi-Factor authentication zabezpečení funkcích a službách. Informace obsažené v tomto článku může být užitečné ve snaze o souladu s obecné Data Protection nařízení (GDPR).

## <a name="scenario"></a>Scénář

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří, Jaderského a baltský moři, jakož i Britské ostrovy. Pro podporu těchto úsilí, získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království 

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu. To zahrnuje osobní identifikovatelné údaje, například jména, adresy, telefonních čísel a informace o kreditní kartě z jeho základní globální zákazníka. Ve všech umístěních zahrnuje také tradiční informace lidských zdrojů, jako jsou adresy, telefonních čísel, daň identifikační čísla a další informace o zaměstnance společnosti. Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů, která zahrnuje osobní údaje ke sledování vztahů se zákazníky aktuální a starší.

Zaměstnanci společnosti přístup k síti ze vzdálených pobočkách společnosti a agenty cesta umístěné po celém světě mají přístup k některým prostředkům společnosti.

## <a name="problem-statement"></a>Popis problému

Společnosti musí ochrany osobních údajů zaměstnanců a zákazníků osobních údajů z útočníci snaží používat ohroženými identity k získání přístupu. Se taky musí zajistěte, aby byl přístup k osobním údajům oprávněným uživatelům s omezeným přístupem jenom na ty, kteří potřebují ho pro svou práci.

## <a name="company-goal"></a>Cílem společnosti

Cílem společnosti je potřeba zajistit přísnou kontrolu přístupu k osobním datům. Je nezbytné, aby identit uživatelů s přístupem k osobním údajům chráněn silné ověřování. Zásady [nejnižší oprávnění] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) musí být vynucená tak, aby oprávněných uživatelů pouze úroveň přístupu, které potřebují a žádné další.

## <a name="solutions"></a>Řešení

Microsoft Azure poskytuje nástroje pro správu identit a přístupu ke společnosti určovat, kdo má přístup k prostředkům, které obsahují osobní údaje.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) spravuje identit a řídí přístup k Azure stejně jako další místní a dalších prostředků cloudu, datům a aplikacím. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) pomáhá správcům Azure, chcete-li minimalizovat počet lidí, kteří mají přístup k určité informace, jako je například osobní údaje. Umožňuje jejich k zjištění, omezte a monitorujte privilegované identity a jejich přístup k prostředkům a k přiřazení dočasné, JIT (JIT) práva správce na oprávněné uživatele. Také poskytuje přehled o uživatelů, kteří mají oprávnění správce AAD.

Činnosti spojené s použitím AAD PIM, patří:

- Povolení Privileged Identity Management pro svůj adresář

- Pomocí řídicího panelu Správce Privileged Identity Management důležité informace na první pohled

- Správa privilegovaných identit (administrators) přidáním nebo odebráním trvalé nebo oprávněné správce ke každé roli

- Konfigurace nastavení aktivace role

- Aktivace role

- Kontrola role aktivity

#### <a name="how-do-i-enable-aad-pim"></a>Povolení AAD PIM

Chcete-li začít používat PIM pro váš adresář, postupujte takto:

1. Přihlaste se k portálu Azure jako globální správce adresáře.

2. Pokud má vaše organizace víc než jeden adresář, vyberte své uživatelské jméno v pravém horním rohu webu Azure Portal. Vyberte adresář, kde budete používat Azure AD Privileged Identity Management.

3. Vyberte **další služby** a použít **filtru** textové pole pro vyhledávání Azure AD Privileged Identity Management.

4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

Po nastavení Azure AD Privileged Identity Management se vždy, když aplikaci otevřete, zobrazí navigační okno.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Další informace a pokyny, Začínáme se službou AAD PIM najdete v tématu [spustit pomocí Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Řízení přístupu Azure na základě rolí

[Řízení přístupu Azure na základě rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) pomáhá Azure správcům spravovat přístup k prostředkům Azure povolením udělení přístupu na základě role přiřazené uživatele. Můžete povinnosti v rámci týmu oddělit a poskytnout pouze takovou úroveň přístupu pro uživatele, skupiny a aplikace, které potřebují k provádění svých úloh.

Přístup na základě role můžete udělit uživatelům, kteří používají portál Azure, nástroje příkazového řádku Azure nebo rozhraní API pro správu Azure.

Další informace o základní informace o Azure RBAC najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Jak lze spravovat Azure RBAC pomocí prostředí PowerShell?

Rutiny prostředí PowerShell můžete použít ke správě Azure RBAC, včetně těchto úloh správy:

- Seznam rolí

- Zjistit, kdo má přístup

- Udělení přístupu

- Odebrání přístupu

- Vytvořit vlastní roli

- Získat akce pro poskytovatele prostředků

- Upravit vlastní roli

- Odstranit vlastní roli

- Vlastní role seznamu

Pokyny o tom, jak spravovat Azure RBAC pomocí prostředí PowerShell najdete v tématu [přístupu na základě Role spravovat pomocí prostředí Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) je řešení dvoustupňové ověření, která pomáhá zabezpečit přístup k datům a aplikacím, při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování prostřednictvím celé řady metod ověřování, včetně ověřování pomocí telefonních hovorů, textových zpráv nebo mobilních aplikací.

Pokud chcete nasadit MFA v cloudu Azure, musíte nejprve povolte a potom zapnout dvoustupňové ověřování pro uživatele.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Povolení Azure a použít vícefaktorové ověřování?

Pokud mají vaši uživatelé licencí, které zahrnují Azure Multi-Factor Authentication, jednoduše muset nakonfigurovat Azure MFA na za jednotlivé uživatele nebo skupiny. 

![Uživatelé povolené ověřování MFA](media/protect-personal-data-identity-access-controls/enable-mfa.png)

Pokud aktuálně nemáte licencí, které budete muset projít procesem určení nejvhodnějšího typu nasazení pro váš scénář. Můžete spustit prohlížením článek s názvem [vybrat řešení pro Azure Multi-Factor Autehntication pro vás](../multi-factor-authentication/multi-factor-authentication-get-started.md). Pokud se rozhodnete, že je potřeba vytvořit aplikace Multi-Factor Authentication server. Můžete spustit pomocí následujících kroků:

1. Vyberte **služby Active Directory** na portálu Azure (přihlášeni jako správce).

2. Vyberte **MFA serveru**

3. Zadejte hodnotu časového limitu. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. Klikněte na tlačítko **uložit**

V tomto okně máte také možnost stažení serveru MFA. Můžete získat další podrobnosti o tom, jak upravit velikost a plánování nasazení najdete v článku [Začínáme s Azure Multi-Factor Authentication server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)

Další pokyny o tom, jak spravovat vaše zprostředkovatel vícefaktorového ověřování najdete v tématu [Začínáme s poskytovatele Azure Multi-Factor Auth.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Jak lze zapnout dvoustupňové ověřování pro uživatele?

Můžete vynutit dvoustupňové ověřování pro všechny přihlášení, nebo můžete vytvořit zásady podmíněného přístupu tak, aby vyžadovala dvoustupňové ověřování jenom v případě, že určité podmínky.

Povolení Azure MFA změnou stavů uživatele je tradiční přístup pro vyžádání dvoustupňové ověřování. Všechny uživatele, kteří povolíte, bude mít stejný požadavek k provedení dvoustupňové ověřování při každém přihlášení. Povolení uživatele potlačí všechny zásady podmíněného přístupu, které může mít vliv na tohoto uživatele.

Povolení Azure MFA se zásadami podmíněného přístupu je flexibilnější přístup pro vyžádání dvoustupňové ověřování. Můžete vytvořit zásady podmíněného přístupu, které platí pro skupiny, jakož i jednotlivé uživatele. S vysokým rizikem skupiny je možné přidělit další omezení než nízkým rizikem skupiny nebo dvoustupňové ověřování můžete vyžaduje se jenom pro vysoce rizikové cloudové aplikace a pro ty, které jsou nízkým rizikem přeskočeno. Podmíněný přístup je však placené funkce služby Azure Active Directory.

Pokud chcete povolit MFA změnou stavu uživatele, postupujte takto:

1. Přihlaste se k portálu Azure jako správce.
2. Přejděte na **Azure Active Directory \> uživatelů a skupin \> všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
4. Vyhledejte uživatele, který chcete povolit pro Azure MFA. Možná bude třeba změnit zobrazení v horní části.
5. Zaškrtněte políčko vedle jména uživatele.
6. Na pravé straně v části Rychlé kroky, zvolte **povolit**.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. Potvrďte výběr v místním okně, které se otevře.  Uživatelé, pro které bylo povoleno vícefaktorové ověřování vyzve k registraci při příštím přihlášení.

Pokud chcete povolit Azure MFA se zásadami podmíněného přístupu, postupujte takto:

1. Přihlaste se k portálu Azure jako správce.

2. Přejděte na **Azure Active Directory \> podmíněného přístupu**.

3. Vyberte **nové zásady**.

4. V části **přiřazení**, vyberte **uživatelů a skupin**. Použití **zahrnout** a **vyloučit** karty se můžete určit, kteří uživatelé a skupiny bude spravovat zásady.

5. V části **přiřazení**, vyberte **cloudových aplikací.** Zvolit **zahrnují všechny cloudové aplikace**.
6.  V části **přístup k ovládacím prvkům**, vyberte **Grant**. Zvolte **vyžadovat vícefaktorové ověřování**.
7.  Zapnout **povolit zásady** k **na** a pak vyberte **Uložit**.

Informace o tom, jak nakonfigurovat nastavení Azure MFA k nastavení upozornění na podvod, vytvořit jednorázové přihlášení, použijte vlastní hlasové zprávy, konfiguraci ukládání do mezipaměti, zadejte důvěryhodné IP adresy, vytvoření hesel aplikací povolení zapamatování vícefaktorového ověřování pro zařízení, které důvěřují uživatele, vyberte metody ověřování, najdete v části [konfigurace nastavení Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Další postup

- [Zabezpečení privilegovaného přístupu ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Nejčastější dotazy ohledně služby Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Na základě rolí řešení potíží s řízení přístupu](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Ochrany identit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
