---
title: Výchozí nastavení zabezpečení služby Azure Active Directory
description: Výchozí zásady zabezpečení, které pomáhají chránit organizace před běžnými útoky
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248852"
---
# <a name="what-are-security-defaults"></a>Co jsou výchozí hodnoty zabezpečení?

Správa zabezpečení může být obtížná, když jsou stále populárnější běžné útoky související s identitou. Mezi tyto útoky patří rozprašování hesel, přehrání a phishing.

Výchozí nastavení zabezpečení ve službě Azure Active Directory (Azure AD) usnadňují zabezpečení a pomáhají chránit vaši organizaci. Výchozí hodnoty zabezpečení obsahují předem nakonfigurovaná nastavení zabezpečení pro běžné útoky. 

Společnost Microsoft zpřístupňuje výchozí nastavení zabezpečení všem uživatelům. Cílem je zajistit, aby všechny organizace měly základní úroveň zabezpečení povolenou bez dodatečných nákladů. Výchozí nastavení zabezpečení zapnete na webu Azure Portal.

![Snímek obrazovky s portálem Azure s přepínačem pro povolení výchozích nastavení zabezpečení](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Pokud váš tenant byl vytvořen na nebo po 22.října 2019, je možné, že dochází k nové chování zabezpečené ve výchozím nastavení a již mají výchozí nastavení zabezpečení povolené ve vašem tenantovi. Ve snaze chránit všechny naše uživatele, výchozí zabezpečení se zavádí do všech nových klientů vytvořených.

Další podrobnosti o tom, proč jsou k dispozici výchozí hodnoty zabezpečení, naleznete v příspěvku na blogu Alexe Weinerta [Představujeme výchozí nastavení zabezpečení](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Jednotná registrace vícefaktorového ověřování

Všichni uživatelé ve vašem tenantovi se musí zaregistrovat pro vícefaktorové ověřování (MFA) ve formě služby Azure Multi-Factor Authentication. Uživatelé mají 14 dní na registraci vícefaktorového ověřování pomocí aplikace Microsoft Authenticator. Po uplynutí 14 dnů se uživatel nebude moci přihlásit, dokud nebude dokončena registrace vícefaktorového ověřování.

Chápeme, že někteří uživatelé mohou být mimo kancelář nebo se nepřihlásí během 14 dnů bezprostředně po povolení výchozího zabezpečení. Chcete-li zajistit, aby každý uživatel má dostatek času na registraci pro vícefaktorové ověřování, 14denní období je jedinečný pro každého uživatele. 14denní období uživatele začíná po prvním úspěšném interaktivním přihlášení po povolení výchozích nastavení zabezpečení.

## <a name="multi-factor-authentication-enforcement"></a>Vynucení vícefaktorového ověřování

### <a name="protecting-administrators"></a>Ochrana správců

Uživatelé s přístupem k privilegovaným účtům mají lepší přístup k vašemu prostředí. Vzhledem k moci, kterou tyto účty mají, byste s nimi měli zacházet se zvláštní péčí. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat silnější formu ověření účtu pro přihlášení. Ve službě Azure AD můžete získat silnější ověření účtu vyžadováním vícefaktorového ověřování.

Po dokončení registrace pomocí vícefaktorového ověřování bude při každém přihlášení vyžadováno následujících devět rolí správce azure ad, které budou vyžadovat další ověřování:

- Globální správce
- Správce SharePointu
- Správce Exchange
- Správce podmíněného přístupu
- Správce zabezpečení
- Správce technické podpory nebo správce hesel
- Správce fakturace
- Správce uživatele
- Správce ověřování

### <a name="protecting-all-users"></a>Ochrana všech uživatelů

Máme tendenci si myslet, že účty správce jsou pouze účty, které potřebují další vrstvy ověřování. Správci mají široký přístup k citlivým informacím a mohou provádět změny nastavení celého předplatného. Útočníci však mají tendenci cílit na koncové uživatele. 

Poté, co tito útočníci získají přístup, mohou požádat o přístup k privilegovaným informacím jménem původního držitele účtu. Mohou dokonce stáhnout celý adresář a provést phishingový útok na celou organizaci. 

Jednou z běžných metod pro zlepšení ochrany pro všechny uživatele je vyžadovat silnější formu ověření účtu, jako je například vícefaktorové ověřování, pro každého. Jakmile uživatelé dokončí registraci vícefaktorového ověřování, budou kdykoli vyzváni k dalšímu ověření.

### <a name="blocking-legacy-authentication"></a>Blokování staršíverze ověřování

Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, Azure AD podporuje celou řadu ověřovacích protokolů, včetně starších verzí ověřování. *Starší verze ověřování* je termín, který odkazuje na požadavek na ověření ze strany:

- Klienti, kteří nepoužívají moderní ověřování (například klient Office 2010).
- Každý klient, který používá starší poštovní protokoly, například IMAP, SMTP nebo POP3.

Dnes většina kompromitující chvatných pokusů o přihlášení pochází ze staršíverze ověřování. Starší verze ověřování nepodporuje vícefaktorové ověřování. I v případě, že máte v adresáři povolenou zásadu vícefaktorového ověřování, může se útočník ověřit pomocí staršího protokolu a obejít vícefaktorové ověřování. 

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi budou blokovány všechny požadavky na ověření provedené starším protokolem. Výchozí nastavení zabezpečení blokuje základní ověřování exchange active sync.

> [!WARNING]
> Před povolením výchozích hodnot zabezpečení se ujistěte, že správci nepoužívají starší ověřovací protokoly. Další informace naleznete v tématu [Jak přejít od starší verze ověřování](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Ochrana privilegovaných akcí

Organizace používají celou řadu služeb Azure spravovaných prostřednictvím rozhraní AZURE Resource Manager API, včetně:

- portál Azure 
- Azure PowerShell 
- Azure CLI

Použití Azure Resource Manager ke správě služeb je vysoce privilegovaná akce. Azure Resource Manager může změnit konfigurace pro celý klient, jako je nastavení služby a fakturace předplatného. Jednofaktorové ověřování je zranitelné vůči různým útokům, jako je phishing a sprej s heslem. 

Je důležité ověřit identitu uživatelů, kteří chtějí získat přístup ke Správci prostředků Azure a aktualizovat konfigurace. Před povolením přístupu ověřte jejich identitu vyžadováním dalšího ověřování.

Po povolení výchozízabezpečení ve vašem tenantovi, každý uživatel, který je přístup k portálu Azure, Azure PowerShell, nebo Azure CLI bude muset dokončit další ověřování. Tato zásada platí pro všechny uživatele, kteří přistupují ke Správci prostředků Azure, ať už jsou to správci nebo uživateli. 

Pokud uživatel není registrován pro vícefaktorové ověřování, bude muset zaregistrovat pomocí aplikace Microsoft Authenticator, aby mohl pokračovat. Nebude poskytnuto 14denní období registrace vícefaktorového ověřování.

> [!NOTE]
> Před2017 Tenanti Exchange Online mají ve výchozím nastavení zakázáno moderní ověřování. Chcete-li se vyhnout možnosti přihlašovací smyčky při ověřování prostřednictvím těchto klientů, musíte [povolit moderní ověřování](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Účet synchronizace služby Azure AD Connect je vyloučen z výchozích hodnot zabezpečení a nebude vyzván k registraci nebo provedení vícefaktorového ověřování. Organizace by neměly používat tento účet pro jiné účely.

## <a name="deployment-considerations"></a>Aspekty nasazování

Následující další důležité informace souvisejí s nasazením výchozích zabezpečení pro vašeho tenanta.

### <a name="authentication-methods"></a>Metody ověřování

Výchozí nastavení zabezpečení umožňují registraci a používání azure vícefaktorového ověřování **pomocí jenom aplikace Microsoft Authenticator s využitím oznámení**. Podmíněný přístup umožňuje použití libovolné metody ověřování, kterou správce zvolí k povolení.

|   | Výchozí nastavení zabezpečení | Podmíněný přístup |
| --- | --- | --- |
| Oznámení prostřednictvím mobilní aplikace | × | × |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |   | × |
| Textová zpráva do telefonu |   | × |
| Volání na telefon |   | × |
| Hesla aplikací |   | X** |

** Hesla aplikací jsou k dispozici pouze v ověřování pomocí vlastního počítače pro jednotlivé uživatele se staršími scénáři ověřování pouze v případě, že je povolit správci.

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup můžete použít ke konfiguraci zásad podobných výchozím hodnotám zabezpečení, ale s větší rozlišovací schopností včetně vyloučení uživatelů, které nejsou k dispozici ve výchozích hodnotách zabezpečení. Pokud používáte podmíněný přístup a máte ve svém prostředí povolené zásady podmíněného přístupu, výchozí nastavení zabezpečení nebudete k dispozici. Pokud máte licenci, která poskytuje podmíněný přístup, ale nemáte žádné zásady podmíněného přístupu povolené ve vašem prostředí, můžete použít výchozí nastavení zabezpečení, dokud nepovolíte zásady podmíněného přístupu. Další informace o licencování Azure AD najdete na [stránce s cenami Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Varovná zpráva, že můžete mít výchozí nastavení zabezpečení nebo podmíněný přístup není obojí](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Tady jsou podrobné pokyny ke konfiguraci ekvivalentních zásad pomocí podmíněného přístupu:

- [Vyžadování MFA pro správce](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Vyžadování MFA pro správu Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokování starší verze ověřování](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Vyžadování MFA pro všechny uživatele](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Vyžadovat registraci Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – vyžaduje ochranu identity Azure AD

## <a name="enabling-security-defaults"></a>Povolení výchozích hodnot zabezpečení

Povolení výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [portálu](https://portal.azure.com) Azure jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Přejděte na **službu Vlastnosti služby** **Azure Active Directory** >.
1. Vyberte **možnost Spravovat výchozí hodnoty zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **Hodnotu Ano**.
1. Vyberte **Uložit**.

## <a name="disabling-security-defaults"></a>Zakázání výchozích hodnot zabezpečení

Organizace, které se rozhodnou implementovat zásady podmíněného přístupu, které nahrazují výchozí hodnoty zabezpečení, musí zakázat výchozí hodnoty zabezpečení. 

![Varovná zpráva zakázat výchozí nastavení zabezpečení pro povolení podmíněného přístupu](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Zakázání výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [portálu](https://portal.azure.com) Azure jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Přejděte na **službu Vlastnosti služby** **Azure Active Directory** >.
1. Vyberte **možnost Spravovat výchozí hodnoty zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **Ne**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Běžné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md)
