---
title: Zásady směrného plánu podmíněného přístupu – Azure Active Directory
description: Zásady podmíněného přístupu podle směrného plánu k ochraně organizací před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767564"
---
# <a name="what-are-baseline-policies"></a>Co jsou zásady směrného plánu?

Zásady směrného plánu jsou sadou předdefinovaných zásad, které pomáhají chránit organizace před mnoha běžnými útoky. Tyto běžné útoky mohou zahrnovat sprej hesla, přehrání a phishing. Zásady směrného plánu jsou k dispozici ve všech edicích Azure AD. Společnost Microsoft zpřístupňuje tyto základní zásady ochrany všem uživatelům, protože útoky založené na identitě byly v posledních několika letech na vzestupu. Cílem těchto čtyř zásad je zajistit, aby všechny organizace měly povolenou základní úroveň zabezpečení bez dalších nákladů.

Správa přizpůsobených zásad podmíněného přístupu vyžaduje licenci Azure AD Premium.

> [!IMPORTANT]
> Zásady směrného plánu jsou zastaralé. Další informace najdete [v tématu Co je nového ve službě Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Základní zásady

![Zásady směrného plánu podmíněného přístupu na webu Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Existují čtyři základní zásady:

* Vyžadovat vícefaktorové finanční pomocí pro správce (preview)
* Ochrana koncového uživatele (náhled)
* Blokovat starší verze ověřování (preview)
* Vyžadovat vícefaktorové řízení pro správu služeb (preview)

Všechny čtyři tyto zásady budou mít vliv na starší toky ověřování, jako jsou POP, IMAP a starší desktopoví klienti Office.

### <a name="exclusions"></a>Vyloučení

Když zásady směrného plánu přešly do počáteční verze Public Preview, existovala možnost vyloučit uživatele ze zásad. Tato funkce se vyvinula prostřednictvím náhledu a byla odstraněna v červenci 2019. Organizace, které již vytvořily vyloučení, mohly nadále udržovat nové uživatele, nemohly k zásadám přidat vyloučení.

### <a name="require-mfa-for-admins-preview"></a>Vyžadovat vícefaktorové finanční pomocí pro správce (preview)

Vzhledem k výkonu a přístupu, který mají účty správce, byste s nimi měli zacházet se zvláštní péčí. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat silnější formu ověření účtu při jejich přihlášení. Ve službě Azure Active Directory můžete získat silnější ověření účtu tím, že budete vyžadovat, aby se správci registrovali a používali azure multifaktorové ověřování.

Vyžadovat vícefaktorové ověřování pro správce (preview) je zásada směrného plánu, která vyžaduje vícefaktorové ověřování (MFA) pro následující role adresáře, které jsou považovány za nejvíce privilegované role Azure AD:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* Správce technické podpory / Správce hesel
* Správce fakturace
* Správce uživatele

Pokud má vaše organizace tyto účty v provozu ve skriptech nebo kódu, zvažte jejich nahrazení [spravovanými identitami](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Ochrana koncového uživatele (náhled)

Vysoce privilegovaní správci nejsou jediní, na které se útoky zaměřují. Špatní herci mají tendenci cílit na běžné uživatele. Po získání přístupu mohou tito chybní aktéři požádat o přístup k privilegovaným informacím jménem původního držitele účtu nebo stáhnout celý adresář a provést phishingový útok na celou organizaci. Jednou z běžných metod ke zlepšení ochrany pro všechny uživatele je vyžadovat silnější formu ověření účtu při zjištění rizikového přihlášení.

**Ochrana koncových uživatelů (preview)** je zásada směrného plánu, která chrání všechny uživatele v adresáři. Povolení této zásady vyžaduje, aby se všichni uživatelé zaregistrovali pro azure multifaktorové ověřování do 14 dnů. Po registraci budou uživatelé vyzváni k vícefaktorové muziky pouze během riskantních pokusů o přihlášení. Ohrožené uživatelské účty jsou blokovány až do resetování hesla a riziko propuštění. 

> [!NOTE]
> Všichni uživatelé, kteří byli dříve označeni jako ohrožení, jsou blokováni, dokud nebude po aktivaci zásad resetováno a propuštěno riziko.

### <a name="block-legacy-authentication-preview"></a>Blokovat starší verze ověřování (preview)

Starší ověřovací protokoly (např. Protokoly IMAP, SMTP, POP3) jsou protokoly běžně používané staršími poštovními klienty k ověření. Starší protokoly nepodporují vícefaktorové ověřování. I v případě, že máte zásadu vyžadující vícefaktorové ověřování pro váš adresář, chybný objekt actor může ověřit pomocí jednoho z těchto starších protokolů a obejít vícefaktorové ověřování.

Nejlepším způsobem, jak chránit účet před škodlivými požadavky na ověření ze strany starších protokolů, je jejich blokování.

Zásady **block legacy authentication (preview)** blokují požadavky na ověření, které jsou prováděny pomocí starších protokolů. K úspěšnému přihlášení pro všechny uživatele je nutné použít moderní ověřování. Ve spojení s ostatními zásadami směrného plánu budou blokovány požadavky pocházející ze starších protokolů. Kromě toho budou všichni uživatelé povinni vícefaktorové financování, kdykoli je to požadováno. Tato zásada neblokuje exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Vyžadovat vícefaktorové řízení pro správu služeb (preview)

Organizace používají celou řadu služeb Azure a spravují je z nástrojů založených na Azure Resource Manageru, jako jsou:

* portál Azure
* Azure PowerShell
* Azure CLI

Použití některého z těchto nástrojů k provádění správy prostředků je vysoce privilegovaná akce. Tyto nástroje mohou změnit konfigurace pro celé předplatné, jako je nastavení služby a fakturace předplatného.

K ochraně privilegovaných akcí bude tato **zásada Vyžadovat vícefaktorové ověřování pro správu služeb (preview)** vyžadovat vícefaktorové ověřování pro všechny uživatele, kteří přistupují k portálu Azure Portal, Azure PowerShell u nebo k rozhraní příkazového příkazu k Azure.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu:

* [Povolení výchozích hodnot zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md)
* [Běžné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
* [Pět kroků k zabezpečení infrastruktury identit](../../security/fundamentals/steps-secure-identity.md)
