---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak Azure AD Identity Protection umožňuje omezit schopnost útočníka zneužít ohroženou identitu nebo zařízení a zabezpečit identitu nebo zařízení, které bylo dříve podezřelé nebo které bylo známo, že by mohlo dojít k ohrožení zabezpečení.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125679"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co je Azure Active Directory Identity Protection?

Azure Active Directory [identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection umožňuje organizacím konfigurovat automatizované odpovědi na zjištěné podezřelé akce související s identitami uživatelů.

## <a name="get-started"></a>Začínáme

Společnost Microsoft má zabezpečené cloudové identity po dobu více než dekády. Pomocí Azure Active Directory Identity Protection můžete ve svém prostředí použít pro zabezpečení identit stejné systémy ochrany, které Microsoft používá.

Pokud útočníci získají přístup k prostředí tím, že ukrást identitu uživatele, dojde k převážné většině narušení zabezpečení. V průběhu let se útočníci stále ještě více dostanou používat při využívání narušení třetích stran a používání sofistikovaných útoků phishing. Jakmile útočník získá přístup k i malým privilegovaným uživatelským účtům, je poměrně snadné získat přístup k důležitým firemním prostředkům prostřednictvím bočního pohybu.

V důsledku toho je potřeba:

- Chránit všechny identity bez ohledu na úroveň oprávnění
- Proaktivní zabránění zneužití ohrožených identit

Zjišťování ohrožených identit není jednoduché. Azure Active Directory používá pro detekci anomálií a podezřelých incidentů, které označují potenciálně ohrožené identity, algoritmy adaptivního strojového učení a heuristiky. Pomocí těchto dat aplikace Identity Protection generuje sestavy a výstrahy, které vám umožní vyhodnotit zjištěné problémy a provádět příslušné akce týkající se zmírnění nebo nápravy.

Azure Active Directory Identity Protection je více než Nástroj pro monitorování a vytváření sestav. Chcete-li chránit identity vaší organizace, můžete nakonfigurovat zásady založené na rizicích, které automaticky reagují na zjištěné problémy v případě dosažení zadané úrovně rizika. Tyto zásady kromě dalších ovládacích prvků podmíněného přístupu, které poskytuje Azure Active Directory a [Enterprise mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), můžou automaticky blokovat nebo iniciovat adaptivní nápravné akce, včetně resetování hesla a vynucování Multi-Factor Authentication.

### <a name="identity-protection-capabilities"></a>Možnosti ochrany identity

**Zjišťování chyb zabezpečení a rizikových účtů:**  

- Poskytování vlastních doporučení pro zlepšení celkového zabezpečení stav zvýrazňováním ohrožení zabezpečení
- Výpočet úrovní rizika přihlašování
- Výpočet úrovní rizika uživatele

**Zkoumání zjištění rizik:**

- Odesílání oznámení pro detekci rizik
- Zkoumání zjištění rizik pomocí relevantních a kontextových informací
- Poskytování základních pracovních postupů pro sledování šetření
- Poskytování snadného přístupu k opravám, jako je resetování hesla

**Zásady podmíněného přístupu na základě rizika:**

- Zásady pro zmírnění rizikových přihlášení blokováním přihlášení nebo vyžadováním výzev pro službu Multi-Factor Authentication
- Zásady pro blokování nebo zabezpečení rizikových uživatelských účtů
- Zásady, které vyžadují, aby si uživatelé zaregistrovali službu Multi-Factor Authentication

## <a name="identity-protection-roles"></a>Role ochrany identity

K vyrovnávání zatížení aktivit správy v rámci implementace ochrany identity můžete přiřadit několik rolí. Azure AD Identity Protection podporuje 3 role adresáře:

| Role | Může | Nelze provést |
| :-- | --- | --- |
| Globální správce | Úplný přístup k Identity Protection a registraci Identity Protection| |
| Správce zabezpečení | Úplný přístup k Identity Protection | Zaregistrování Identity Protection, resetování hesel pro uživatele |
| Čtenář zabezpečení | Přístup k Identity Protection jen pro čtení | Zaregistrování Identity Protection, napravení uživatelů, konfigurace zásad, resetování hesel |

Další podrobnosti najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) .

## <a name="detection"></a>Detekce

### <a name="vulnerabilities"></a>Ohrožení zabezpečení

Azure Active Directory Identity Protection analyzuje vaši konfiguraci a detekuje ohrožení zabezpečení, která mohou mít dopad na identity vašeho uživatele. Další podrobnosti najdete v tématu [chyby zabezpečení zjištěné nástrojem Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-detections"></a>Detekce rizik

Azure Active Directory používá k detekci podezřelých akcí, které souvisejí s identitami vašich uživatelů, adaptivní algoritmy strojového učení a heuristiky. Systém vytvoří záznam pro každou zjištěnou podezřelou akci. Tyto záznamy se označují také jako detekce rizik.  
Další podrobnosti najdete v tématu [Azure Active Directory detekci rizik](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Šetření

Vaše cesta prostřednictvím Identity Protection obvykle začíná řídicím panelem Identity Protection.

![Náprava](./media/overview/1000.png "Náprava")

Tento řídicí panel poskytuje přístup k:

- Sestavy, jako jsou **Uživatelé označení příznakem rizika**, **detekce rizik** a **ohrožení zabezpečení**
- Nastavení, jako je například konfigurace **zásad zabezpečení**, **oznámení** a **Registrace služby Multi-Factor Authentication**

Obvykle je výchozím bodem pro šetření, což je proces kontroly aktivit, protokolů a dalších relevantních informací souvisejících s detekcí rizik, rozhodování o tom, zda jsou kroky pro nápravu nebo zmírnění problémů nezbytné a jak byla identita ohrožení zabezpečení a pochopení způsobu použití ohrožené identity.

Své vyšetřovací aktivity můžete spojit s oznámeními [](notifications.md) Azure Active Directory odesílají e-maily na e-mail.

## <a name="policies"></a>Zásady

K implementaci automatizovaných odpovědí vám Azure Active Directory Identity Protection poskytne tři zásady:

- [Zásady registrace Multi-Factor Authentication](howto-mfa-policy.md)
- [Zásady rizik uživatelů](howto-user-risk-policy.md)
- [Zásady rizik přihlašování](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Další kroky

- [Kanál 9: Azure AD a zobrazení identity: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Povolení Azure Active Directory Identity Protection](enable.md)
