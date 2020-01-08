---
title: Výchozí nastavení zabezpečení Azure Active Directory
description: Výchozí zásady zabezpečení, které vám pomůžou chránit organizace před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83a839d75757bcee14d7f696d2d11d1d7d8fa4cc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422844"
---
# <a name="what-are-security-defaults"></a>Co jsou výchozí nastavení zabezpečení?

Správa zabezpečení může být obtížné, pokud se běžné útoky související s identitou stanou více a mnohem populární. Mezi tyto útoky patří sprej, přehrávání a útoky prostřednictvím hesla.

Výchozí nastavení zabezpečení v Azure Active Directory (Azure AD) usnadňuje zabezpečení a lepší ochranu vaší organizace. Výchozí nastavení zabezpečení obsahují předem nakonfigurovaná nastavení zabezpečení pro běžné útoky. 

Microsoft zpřístupňuje výchozí nastavení zabezpečení všem uživatelům. Cílem je zajistit, aby měly všechny organizace základní úroveň zabezpečení povoleno bez dalších poplatků. Výchozí nastavení zabezpečení můžete zapnout v Azure Portal.

![Snímek obrazovky Azure Portal s přepínačem pro povolení výchozích hodnot zabezpečení](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Ve vašem tenantovi budou zapnuté následující konfigurace zabezpečení. 

## <a name="unified-multi-factor-authentication-registration"></a>Registrace sjednocené Multi-Factor Authentication

Všichni uživatelé ve vašem tenantovi musí zaregistrovat službu Multi-Factor Authentication (MFA) ve formě služby Azure Multi-Factor Authentication. K registraci Multi-Factor Authentication pomocí aplikace Microsoft Authenticator mají uživatelé 14 dní. Po uplynutí 14 dnů se uživatel nebude moct přihlásit, dokud se nedokončí Multi-Factor Authentication registrace.

Chápeme, že někteří uživatelé můžou být mimo kancelář nebo se nebudou přihlašovat během 14 dnů hned po povolení výchozích hodnot zabezpečení. Aby bylo zajištěno, že každý uživatel má dostatek času na registraci Multi-Factor Authentication, je období 14 dní pro každého uživatele jedinečné. Po povolení výchozích hodnot zabezpečení začíná období 14 dní uživatele od prvního úspěšného interaktivního přihlášení.

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication vynucení

### <a name="protecting-administrators"></a>Ochrana správců

Uživatelé s přístupem k privilegovaným účtům mají zvýšený přístup k vašemu prostředí. Vzhledem k napájení těchto účtů byste je měli považovat za zvláštní péči. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat pro přihlášení silnější formu ověření účtu. V Azure AD můžete získat silnější ověření účtu tím, že budete vyžadovat Multi-Factor Authentication.

Po dokončení registrace u Multi-Factor Authentication se při každém přihlášení budou vyžadovat následující devět rolí správce Azure AD:

- Globální správce
- Správce SharePointu
- Správce Exchange
- Správce podmíněného přístupu
- Správce zabezpečení
- Správce helpdesku nebo správce hesel
- Správce fakturace
- Správce uživatele
- Správce ověřování

### <a name="protecting-all-users"></a>Ochrana všech uživatelů

Doporučujeme, abyste si myslíte, že účty správců jsou jedinými účty, které vyžadují další vrstvy ověřování. Správci mají rozsáhlý přístup k citlivým informacím a můžou provádět změny nastavení pro celé předplatné. Ale útočníci se zaměřují na koncové uživatele. 

Po získání přístupu pro tyto útočníky můžou požádat o přístup k privilegovaným informacím jménem původního držitele účtu. Můžou si dokonce stáhnout celý adresář a udělat tak útok útoku phishing na celou organizaci. 

Jednou z běžných metod pro zlepšení ochrany pro všechny uživatele je vyžadovat silnější formu ověření účtu, například Multi-Factor Authentication pro každého. Jakmile se uživatelé dokončí Multi-Factor Authentication registraci, budou vyzváni k dalšímu ověřování, kdykoli to bude nutné.

### <a name="blocking-legacy-authentication"></a>Blokování starších verzí ověřování

Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, Azure AD podporuje nejrůznější ověřovací protokoly, včetně starší verze ověřování. *Starší verze ověřování* je termín, který odkazuje na žádost o ověření, kterou provedla:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klienta Office 2010).
- Každý klient používající starší e-mailové protokoly, jako jsou IMAP, SMTP nebo POP3.

V současné době většina neúspěšných pokusů o přihlášení pocházela ze staršího ověřování. Starší verze ověřování nepodporuje Multi-Factor Authentication. I v případě, že máte ve svém adresáři povolené zásady Multi-Factor Authentication, útočník se může ověřit pomocí staršího protokolu a Multi-Factor Authentication obcházení. 

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi budou všechny požadavky na ověření provedené starším protokolem blokované. Výchozí nastavení zabezpečení neblokuje Exchange ActiveSync.

> [!WARNING]
> Než povolíte výchozí nastavení zabezpečení, zajistěte, aby vaši správci nepoužívali starší ověřovací protokoly. Další informace najdete v tématu [Jak přejít pryč ze starší verze ověřování](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Ochrana privilegovaných akcí

Organizace používají různé služby Azure spravované prostřednictvím rozhraní Azure Resource Manager API, včetně:

- Portál Azure 
- Azure PowerShell 
- Azure CLI

Použití Azure Resource Manager ke správě služeb je vysoce privilegovaná akce. Azure Resource Manager může měnit konfigurace v rámci tenanta, jako je například nastavení služby a fakturace předplatného. Jednotné vícefaktorové ověřování je zranitelné vůči nejrůznějším útokům, jako je útok phishing a heslo. 

Je důležité ověřit identitu uživatelů, kteří chtějí získat přístup k Azure Resource Manager a aktualizovat konfigurace. Než povolíte přístup, ověříte jejich identitu tím, že budete vyžadovat další ověřování.

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi bude nutné, aby každý uživatel, který přistupuje k Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI, dokončil další ověřování. Tato zásada platí pro všechny uživatele, kteří přistupují k Azure Resource Manager, ať už se jedná o správce nebo uživatele. 

Pokud uživatel není zaregistrován pro Multi-Factor Authentication, bude uživatel vyzván k registraci pomocí aplikace Microsoft Authenticator, aby bylo možné pokračovat. K dispozici není žádné období registrace Multi-Factor Authentication za 14 dní.

> [!NOTE]
> Účet synchronizace Azure AD Connect je vyloučený z výchozích hodnot zabezpečení a nebude vyzván k registraci nebo provedení vícefaktorového ověřování. Organizace by tento účet neměli používat pro jiné účely.

## <a name="deployment-considerations"></a>Aspekty nasazování

K nasazení výchozích hodnot zabezpečení pro vašeho tenanta se vztahují následující další požadavky.

### <a name="authentication-methods"></a>Metody ověření

Výchozí nastavení zabezpečení umožňuje registraci a používání služby Azure Multi-Factor Authentication **jenom pomocí Microsoft Authenticator aplikace s oznámeními**. Podmíněný přístup umožňuje použití libovolné metody ověřování, kterou správce zvolí k povolení.

|   | Výchozí nastavení zabezpečení | Podmíněný přístup |
| --- | --- | --- |
| Oznámení přes mobilní aplikaci | × | × |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |   | × |
| Textová zpráva na telefon |   | × |
| Telefonní hovor |   | × |
| Hesla aplikací |   | × * * |

\* * Hesla aplikací jsou k dispozici pouze v případě MFA pro jednotlivé uživatele se staršími scénáři ověřování, pokud jsou povolena správci.

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup můžete použít ke konfiguraci zásad, které se podobají výchozím hodnotám zabezpečení, ale s větší členitosti včetně vylučování uživatelů, které nejsou dostupné ve výchozím nastavení zabezpečení. Pokud používáte podmíněný přístup a ve vašem prostředí máte povolené zásady podmíněného přístupu, výchozí nastavení zabezpečení nebude k dispozici. Pokud máte licenci, která poskytuje podmíněný přístup, ale nemáte ve svém prostředí povolené žádné zásady podmíněného přístupu, budete mít k dispozici výchozí nastavení zabezpečení, dokud nepovolíte Zásady podmíněného přístupu. Další informace o licencování Azure AD najdete na stránce s [cenami služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Zpráva s upozorněním, že je možné použít výchozí nastavení zabezpečení nebo podmíněný přístup ne obojí](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Tady jsou podrobné návody, jak můžete pomocí podmíněného přístupu nakonfigurovat ekvivalentní zásady:

- [Vyžadovat MFA pro správce](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Vyžadovat vícefaktorové ověřování pro správu Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokovat starší verze ověřování](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Vyžadovat MFA pro všechny uživatele](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Vyžadovat registraci Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – vyžaduje Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>Povolení výchozích hodnot zabezpečení

Povolení výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Vyhledejte **Azure Active Directory** > **vlastnosti**.
1. Vyberte **Spravovat výchozí nastavení zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **Ano**.
1. Vyberte **Uložit**.

## <a name="disabling-security-defaults"></a>Zakazování výchozích hodnot zabezpečení

Organizace, které se rozhodnou implementovat zásady podmíněného přístupu, které nahrazují výchozí hodnoty zabezpečení, musí zakázat výchozí nastavení zabezpečení. 

![Zpráva upozornění zakázat výchozí nastavení zabezpečení pro povolení podmíněného přístupu](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Zakázání výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Vyhledejte **Azure Active Directory** > **vlastnosti**.
1. Vyberte **Spravovat výchozí nastavení zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **ne**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md)
