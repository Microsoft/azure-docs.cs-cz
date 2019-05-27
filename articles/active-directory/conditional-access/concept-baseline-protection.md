---
title: Zásady ochrany směrného plánu podmíněného přístupu – Azure Active Directory
description: Základní zásady podmíněného přístupu k ochraně organizací před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003293"
---
# <a name="what-are-baseline-policies"></a>Co jsou zásady na směrný plán?

Zásady na směrný plán představují sadu předdefinovaných zásad pomoc při ochraně organizací před mnoha útoky běžné. Tyto běžnými útoky mohou zahrnovat heslo zařízení, opakování a útoky phishing. Zásady na směrný plán jsou k dispozici ve všech edicích služby Azure AD. Zpřístupňuje tyto standardní hodnoty zásady ochrany k dispozici všem uživatelům vzhledem k tomu, že byla založená na identitě útoky na vzestupu během posledních několika let. Cílem tyto čtyři zásady je zajistit, že všechny organizace mají úroveň standardních hodnot zabezpečení povoleno bez dalších poplatků.  

Správa zásad podmíněného přístupu přizpůsobené vyžaduje licenci Azure AD Premium.

## <a name="baseline-policies"></a>Základní zásady

![Základní zásady podmíněného přístupu na webu Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Existují čtyři zásady standardních hodnot, které organizacím umožňují:

* [Vyžadovat vícefaktorové ověřování pro správce](howto-baseline-protect-administrators.md)
* [Ochrana koncového uživatele (preview)](howto-baseline-protect-end-users.md)
* [Blok starší verze ověřování (preview)](howto-baseline-protect-legacy-auth.md)
* [Vyžadovat vícefaktorové ověřování pro službu správy (preview)](howto-baseline-protect-azure.md)

Všechny čtyři tyto zásady ovlivní toky starší verze ověřování jako POP, IMAP a starší klienti Office klasické pracovní plochy.

### <a name="require-mfa-for-admins"></a>Vyžadování MFA pro správce

Kvůli výkonu a přístupu, které mají účty správců by měly zpracovávat s zvláštní pozornost. Je běžným způsobem zlepšit ochranu privilegovaných účtů tak, aby vyžadovala silnější formu ověření účtu, když se používají k přihlášení. Ve službě Azure Active Directory získáte silnější ověření účtu tak, že vyžaduje správcům zaregistrujte a použijte ověřování Azure Multi-Factor Authentication.

[Vyžadovat vícefaktorové ověřování pro správce](howto-baseline-protect-administrators.md) je základní zásady, která vyžaduje vícefaktorové ověřování (MFA) pro následující role adresáře, považuje se nejvíce privilegované role Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* Správce technické podpory nebo správce hesel
* Správce fakturace
* Správce uživatelů

Pokud má vaše organizace tyto účty používané v skripty a kód, zvažte nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete vyloučit konkrétní uživatelské účty z základní zásady.

### <a name="end-user-protection-preview"></a>Ochrana koncového uživatele (preview)

Vysoce privilegovaných správců nejsou pouze ty cílí útoky. Nesprávnými účastníky často Představujeme běžným uživatelům. Po získání přístupu, tyto nesprávnými účastníky můžete požádat o přístup k privilegovaným informace jménem původního vlastníka účtu nebo stáhnout celý adresář a provést útok phishing v celé organizaci. Jeden běžnou metodu ke zlepšení ochrany pro všechny uživatele je tak, aby vyžadovala silnější formu ověření účtu, když se zjistí rizikových přihlášení.

**Ochrana koncového uživatele (preview)** je základní zásady, které chrání všechny uživatele v adresáři. Když se tyto zásady vyžaduje všichni uživatelé k registraci pro ověřování Azure Multi-Factor Authentication do 14 dnů. Po registraci, budou uživatelé vyzváni pro vícefaktorové ověřování pouze během rizikové pokusů o přihlášení. Ohrožení uživatelských účtů jsou blokovány, dokud heslo resetovat a rizika propouštění.

### <a name="block-legacy-authentication-preview"></a>Blok starší verze ověřování (preview)

Starší verze ověřovací protokoly (ex: IMAP, SMTP, POP3) protokoly, obvykle využívá starší klienti e-mailu k ověření. Protokoly starší verze nepodporují ověření službou Multi-Factor Authentication. I v případě, že máte zásadu vyžadující ověřování službou Multi-Factor Authentication pro váš adresář, chybný actor můžete ověřit pomocí některé z těchto starších verzí protokolů a obejít ověřování Multi-Factor Authentication.

Nejlepší způsob, jak chránit váš účet ze škodlivých ověřování požadavků od starších verzí protokolů je zablokovat.

**Blok starší verze ověřování (preview)** základní zásady blokují žádosti o ověření, které jsou k použití starších protokolů. Moderní ověřování, musí využívat pro úspěšné přihlášení pro všechny uživatele. Používá ve spojení se zásadami jiné standardní hodnoty, než dorazí požadavky od starších verzí protokolů se zablokuje. Kromě toho všichni uživatelé budou muset vícefaktorové ověřování pokaždé, když se vyžaduje. Tato zásada nedochází k blokování protokolu Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Vyžadovat vícefaktorové ověřování pro službu správy (preview)

Organizace používat širokou škálu služeb Azure a je spravovat pomocí nástrojů založené na Azure Resource Manageru, jako je:

* portál Azure
* Azure PowerShell
* Azure CLI

Pomocí kteréhokoli z těchto nástrojů k provedení správy prostředků je vysoce privilegované akce. Tyto nástroje můžete změnit konfigurace v rámci celé předplatné, jako je například nastavení služby a fakturace předplatného.

Pro ochranu privilegovaných akcí to **vyžadovat vícefaktorové ověřování pro správu service (preview)** zásad bude vyžadovat vícefaktorové ověřování pro všechny uživatele, kteří používají Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="enable-a-baseline-policy"></a>Povolit zásady směrný plán

Pokud chcete povolit zásady směrný plán:

1. Přihlaste se k **webu Azure portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte do **Azure Active Directory** > **podmíněného přístupu**.
1. V seznamu zásad vyberte základní zásady, které chcete povolit.
1. Nastavte **povolit zásady** k **na**.
1. Klikněte na Uložit.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Zabezpečení vaší infrastruktury identit v pěti krocích](../../security/azure-ad-secure-steps.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
* [Vyžadovat vícefaktorové ověřování pro správce](howto-baseline-protect-administrators.md)
* [Ochrana koncového uživatele (preview)](howto-baseline-protect-end-users.md)
* [Blok starší verze ověřování (preview)](howto-baseline-protect-legacy-auth.md)
* [Vyžadovat vícefaktorové ověřování pro službu správy (preview)](howto-baseline-protect-azure.md)