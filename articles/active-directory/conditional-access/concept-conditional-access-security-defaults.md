---
title: Výchozí nastavení zabezpečení Azure Active Directory
description: Výchozí zásady zabezpečení určené k ochraně organizací před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453040"
---
# <a name="what-are-security-defaults"></a>Co jsou výchozí nastavení zabezpečení?

Správa zabezpečení může být obtížné, pokud jsou běžné útoky související s identitou, jako je třeba sprej, přehrávání a útoky prostřednictvím hesla, lepší a oblíbenější. Vytvoření jednoduššího způsobu, jak zajistit větší zabezpečení vaší organizace před těmito běžnými útoky, je cílem výchozích hodnot zabezpečení v Azure Active Directory (AD). Výchozí nastavení zabezpečení usnadňuje zabezpečení a pomáhá chránit vaši organizaci před běžnými útoky. Výchozí hodnoty zabezpečení obsahují předem nakonfigurovaná nastavení zabezpečení pro tyto běžné útoky. Microsoft zpřístupňuje výchozí nastavení zabezpečení všem uživatelům. Cílem je zajistit, aby měly všechny organizace základní úroveň zabezpečení povoleno bez dalších poplatků.

![Snímek obrazovky s novým prostředím uživatelského rozhraní výchozích hodnot zabezpečení](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Ve vašem tenantovi budou zapnuté následující konfigurace zabezpečení. 

## <a name="unified-mfa-registration"></a>Jednotná registrace MFA

Všichni uživatelé ve vašem tenantovi budou muset zaregistrovat pro Azure Multi-Factor Authentication (MFA). Uživatelé budou mít 14 dnů na registraci pro Azure MFA pomocí aplikace Microsoft Authenticator. Po uplynutí 14 dnů se uživatel nebude moct přihlásit, dokud se nedokončí registrace MFA.

Chápeme, že někteří uživatelé můžou být mimo kancelář a/nebo se nebudou přihlašovat během 14 dnů hned po povolení výchozích hodnot zabezpečení. Aby bylo zajištěno, že každý uživatel má dostatek času na registraci MFA, je období 14 dní pro každého uživatele jedinečné. Po dobu 14 dní uživatele začíná po prvním úspěšném interaktivním přihlášení, když je povolené výchozí nastavení zabezpečení.

## <a name="mfa-enforcement"></a>Vynucení MFA

### <a name="protecting-administrators"></a>Ochrana správců

Uživatelé s přístupem k privilegovaným účtům mají zvýšený přístup k vašemu prostředí. Vzhledem k napájení těchto účtů byste je měli považovat za zvláštní péči. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat při přihlášení silnější formu ověření účtu. V Azure Active Directory můžete získat silnější ověření účtu tím, že budete vyžadovat vícefaktorové ověřování.

Po dokončení registrace MFA se při každém přihlášení k jednomu z následujících devět rolí správce Azure AD bude vyžadovat, aby se vícefaktorové ověřování provádělo.

- Globální správce
- Správce SharePointu
- Správce Exchange
- Správce podmíněného přístupu
- Správce zabezpečení
- Správce helpdesku/správce hesel
- Správce fakturace
- Správce uživatele
- Správce ověřování

### <a name="protecting-all-users"></a>Ochrana všech uživatelů

Doporučujeme, abyste si myslíte, že účty správců jsou jedinými účty, které potřebují ochranu pomocí vícefaktorového ověřování (MFA). Správci mají rozsáhlý přístup k citlivým informacím a můžou provádět změny nastavení pro celé předplatné. Chybné objekty actor ale mají za cíl koncové uživatele. Po získání přístupu můžou tyto vadné aktéry požádat o přístup k privilegovaným informacím jménem původního držitele účtu nebo stáhnout celý adresář, aby se v celé organizaci prováděl útok na útok phishing. Jednou z běžných metod, jak zlepšit ochranu pro všechny uživatele, je vyžadovat silnější formu ověření účtu, jako je vícefaktorové ověřování (MFA) pro všechny. Po dokončení registrace MFA se uživatelům zobrazí výzva k ověřování MFA, kdykoli to bude nutné.

### <a name="blocking-legacy-authentication"></a>Blokování starších verzí ověřování

Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, Azure Active Directory (Azure AD) podporuje širokou škálu ověřovacích protokolů, včetně starších verzí ověřování. Starší verze ověřování je termín, který odkazuje na žádost o ověření, kterou provedla:

- Starší klienti Office, kteří nepoužívají moderní ověřování (například klient Office 2010)
- Každý klient, který používá starší e-mailové protokoly, jako je IMAP/SMTP/POP3

V současné době většina všech neúspěšných pokusů o přihlášení pocházela ze staršího ověřování. Starší verze ověřování nepodporuje službu Multi-Factor Authentication (MFA). I v případě, že máte ve svém adresáři povolené zásady vícefaktorového ověřování, může se špatný objekt actor ověřit pomocí starší verze protokolu a obejít MFA. Po povolení výchozích hodnot zabezpečení ve vašem tenantovi se zablokují všechny požadavky na ověření provedené starším protokolem. Výchozí nastavení zabezpečení neblokuje Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Ochrana privilegovaných akcí

Organizace používají různé služby Azure spravované prostřednictvím rozhraní Azure Resource Manager API, včetně:

- Portál Azure 
- Azure PowerShell 
- Azure CLI

Použití Azure Resource Manager ke správě služeb je vysoce privilegovaná akce. Azure Resource Manager může měnit konfigurace v rámci tenanta, jako je například nastavení služby a fakturace předplatného. Ověřování jedním faktorem je zranitelné vůči nejrůznějším útokům, jako je útok phishing a heslo. Proto je důležité ověřit identitu uživatelů, kteří chtějí přistupovat k konfiguracím Azure Resource Manager a aktualizace, a to tak, že před povolením přístupu povolíte vícefaktorové ověřování.

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi bude nutné, aby všichni uživatelé přistupující k Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI dokončili službu Multi-Factor Authentication. Tato zásada platí pro všechny uživatele, kteří přistupují k Azure Resource Manager bez ohledu na to, jestli se jedná o správce nebo uživatele. Pokud uživatel není zaregistrován pro vícefaktorové ověřování, uživatel bude muset zaregistrovat pomocí aplikace Microsoft Authenticator, aby bylo možné pokračovat. K dispozici není žádné 14 dní registračního období MFA.

## <a name="deployment-considerations"></a>Aspekty nasazování

Zde jsou některé další okolnosti týkající se nasazení výchozích hodnot zabezpečení pro vašeho tenanta.

### <a name="legacy-protocols"></a>Starší protokoly

Starší verze ověřovacích protokolů (IMAP, SMTP, POP3 atd.) jsou používány e-mailové klienty k provádění požadavků na ověření. Tyto protokoly vícefaktorové ověřování nepodporují. Většina kompromisů, které společnost Microsoft uvidí, je způsobena nesprávnými aktéry, které provádějí útoky proti starším protokolům, které se pokoušejí obejít MFA. Aby bylo zajištěno, že při přihlašování k účtu správce je vyžadováno MFA, nemůžou nepřístupné objekty actor obejít MFA, výchozí hodnoty zabezpečení zablokují všechny požadavky na ověření provedené u účtů správce ze starších protokolů

> [!WARNING]
> Než povolíte toto nastavení, zajistěte, aby vaši správci nepoužívali starší protokoly pro ověřování. Další informace najdete v článku [o tom, jak opustit starší verze ověřování](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup se dá použít ke konfiguraci zásad, které poskytují stejné chování jako u výchozích hodnot zabezpečení. Pokud používáte podmíněný přístup a ve vašem prostředí máte povolené zásady podmíněného přístupu, výchozí nastavení zabezpečení nebudou k dispozici. Pokud máte licenci, která poskytuje podmíněný přístup, ale nemáte ve svém prostředí povolené žádné zásady podmíněného přístupu, budete mít k dispozici výchozí nastavení zabezpečení, dokud nepovolíte Zásady certifikační autority.

![Výchozí nastavení zabezpečení nebo podmíněný přístup ne obojí](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Tady jsou podrobné návody, jak pomocí podmíněného přístupu nakonfigurovat ekvivalentní zásady:

- [Vyžadovat MFA pro správce](howto-conditional-access-policy-admin-mfa.md)
- [Vyžadovat vícefaktorové ověřování pro správu Azure](howto-conditional-access-policy-azure-management.md)
- [Blokovat starší verze ověřování](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Povolení výchozích hodnot zabezpečení

Povolení výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)@no__t – 1AS správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Přejít na **Azure Active Directory** **vlastnosti**   @ no__t-2
1. Vybrat **Spravovat výchozí nastavení zabezpečení**
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **Ano**.
1. Klikněte na Uložit.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Co je podmíněný přístup?](overview.md)
